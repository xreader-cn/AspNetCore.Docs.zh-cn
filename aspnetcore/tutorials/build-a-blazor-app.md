---
title: 生成 Blazor 待办事项列表应用
author: guardrex
description: 逐步生成 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 174a8e561701bb3ebd68ed05e42dfc3d70a9b450
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176223"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="60d62-103">生成 Blazor 待办事项列表应用</span><span class="sxs-lookup"><span data-stu-id="60d62-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="60d62-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="60d62-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="60d62-105">本教程演示如何生成和修改 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="60d62-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="60d62-106">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="60d62-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="60d62-107">创建待办事项列表 Blazor 应用项目</span><span class="sxs-lookup"><span data-stu-id="60d62-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="60d62-108">修改 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="60d62-108">Modify Razor components</span></span>
> * <span data-ttu-id="60d62-109">在组件中使用事件处理和数据绑定</span><span class="sxs-lookup"><span data-stu-id="60d62-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="60d62-110">在 Blazor 应用中使用依赖关系注入 (DI) 和路由</span><span class="sxs-lookup"><span data-stu-id="60d62-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="60d62-111">在本教程结束时，你将拥有一个正常运行的待办事项列表应用。</span><span class="sxs-lookup"><span data-stu-id="60d62-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

1. <span data-ttu-id="60d62-112">在命令行界面中创建名为 `TodoList` 的新 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="60d62-112">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="60d62-113">上述命令将创建一个名为 `TodoList` 的文件夹来保存应用。</span><span class="sxs-lookup"><span data-stu-id="60d62-113">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="60d62-114">使用以下命令将目录切换到 `TodoList` 文件夹：</span><span class="sxs-lookup"><span data-stu-id="60d62-114">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="60d62-115">使用以下命令向 `Pages` 文件夹中的应用添加一个新的 `Todo` Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="60d62-115">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor<span data-ttu-id="60d62-116"> 组件文件名要求首字母大写，因此请确认 `Todo` 组件的文件名以大写字母 `T` 开头。</span><span class="sxs-lookup"><span data-stu-id="60d62-116"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="60d62-117">在 `Pages/Todo.razor` 中为组件提供初始标记：</span><span class="sxs-lookup"><span data-stu-id="60d62-117">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="60d62-118">将 `Todo` 组件添加到导航栏。</span><span class="sxs-lookup"><span data-stu-id="60d62-118">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="60d62-119">`NavMenu` 组件 (`Shared/NavMenu.razor`) 用于应用的布局。</span><span class="sxs-lookup"><span data-stu-id="60d62-119">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="60d62-120">布局是可以避免应用中出现重复内容的组件。</span><span class="sxs-lookup"><span data-stu-id="60d62-120">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="60d62-121">通过在 `Shared/NavMenu.razor` 文件中的现有列表项下添加以下列表项标记，为 `Todo` 组件添加一个 `<NavLink>` 元素：</span><span class="sxs-lookup"><span data-stu-id="60d62-121">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="60d62-122">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="60d62-122">Rebuild and run the app.</span></span> <span data-ttu-id="60d62-123">访问新的“待办事项”页面，确认指向 `Todo` 组件的链接有效。</span><span class="sxs-lookup"><span data-stu-id="60d62-123">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="60d62-124">向项目的根目录添加 `TodoItem.cs` 文件，以保存一个用于表示待办项的类。</span><span class="sxs-lookup"><span data-stu-id="60d62-124">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="60d62-125">为 `TodoItem` 类使用以下 C# 代码：</span><span class="sxs-lookup"><span data-stu-id="60d62-125">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="60d62-126">返回到 `Todo` 组件 (`Pages/Todo.razor`)：</span><span class="sxs-lookup"><span data-stu-id="60d62-126">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="60d62-127">在 `@code` 块中为待办项添加一个字段。</span><span class="sxs-lookup"><span data-stu-id="60d62-127">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="60d62-128">`Todo` 组件使用此字段来维护待办项列表的状态。</span><span class="sxs-lookup"><span data-stu-id="60d62-128">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="60d62-129">添加无序列表标记和 `foreach` 循环，以将每个待办项呈现为列表项 (`<li>`)。</span><span class="sxs-lookup"><span data-stu-id="60d62-129">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="60d62-130">该应用需要 UI 元素来将待办项添加到列表。</span><span class="sxs-lookup"><span data-stu-id="60d62-130">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="60d62-131">在未排序列表 (`<ul>...</ul>`) 下方添加一个文本输入 (`<input>`) 和一个按钮 (`<button>`)：</span><span class="sxs-lookup"><span data-stu-id="60d62-131">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="60d62-132">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="60d62-132">Rebuild and run the app.</span></span> <span data-ttu-id="60d62-133">选择“`Add todo`”按钮时没有任何反应，因为没有事件处理程序连接到该按钮。</span><span class="sxs-lookup"><span data-stu-id="60d62-133">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="60d62-134">向 `Todo` 组件添加 `AddTodo` 方法，并使用 `@onclick` 属性注册该方法以选择按钮。</span><span class="sxs-lookup"><span data-stu-id="60d62-134">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="60d62-135">选择按钮时，会调用 `AddTodo` C# 方法：</span><span class="sxs-lookup"><span data-stu-id="60d62-135">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="60d62-136">要获得新待办项标题，请在 `@code` 块顶部添加 `newTodo` 字符串字段，并使用 `<input>` 元素中的 `bind` 属性将其绑定到文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="60d62-136">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="60d62-137">更新 `AddTodo` 方法，将具有指定标题的 `TodoItem` 添加到列表。</span><span class="sxs-lookup"><span data-stu-id="60d62-137">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="60d62-138">通过将 `newTodo` 设置为空字符串来清除文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="60d62-138">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="60d62-139">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="60d62-139">Rebuild and run the app.</span></span> <span data-ttu-id="60d62-140">在待办项列表中添加一些待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="60d62-140">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="60d62-141">每个待办项的标题文本都可以编辑，复选框可以帮助用户跟踪已完成的项。</span><span class="sxs-lookup"><span data-stu-id="60d62-141">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="60d62-142">为每个待办项添加一个复选框输入，并将它的值绑定到 `IsDone` 属性。</span><span class="sxs-lookup"><span data-stu-id="60d62-142">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="60d62-143">将 `@todo.Title` 更改为绑定到 `@todo.Title` 的 `<input>` 元素：</span><span class="sxs-lookup"><span data-stu-id="60d62-143">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="60d62-144">若要验证这些值是否已绑定，请更新 `<h3>` 标头以显示尚未完成的待办项计数（`IsDone` 是 `false`）。</span><span class="sxs-lookup"><span data-stu-id="60d62-144">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="60d62-145">已完成的 `Todo` 组件 (`Pages/Todo.razor`)：</span><span class="sxs-lookup"><span data-stu-id="60d62-145">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="60d62-146">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="60d62-146">Rebuild and run the app.</span></span> <span data-ttu-id="60d62-147">添加待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="60d62-147">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="60d62-148">后续步骤</span><span class="sxs-lookup"><span data-stu-id="60d62-148">Next steps</span></span>

<span data-ttu-id="60d62-149">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="60d62-149">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="60d62-150">创建待办事项列表 Blazor 应用项目</span><span class="sxs-lookup"><span data-stu-id="60d62-150">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="60d62-151">修改 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="60d62-151">Modify Razor components</span></span>
> * <span data-ttu-id="60d62-152">在组件中使用事件处理和数据绑定</span><span class="sxs-lookup"><span data-stu-id="60d62-152">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="60d62-153">在 Blazor 应用中使用依赖关系注入 (DI) 和路由</span><span class="sxs-lookup"><span data-stu-id="60d62-153">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="60d62-154">了解用于 ASP.NET Core Blazor 的工具：</span><span class="sxs-lookup"><span data-stu-id="60d62-154">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
