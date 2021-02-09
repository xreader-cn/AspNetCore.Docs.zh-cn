---
title: 生成 Blazor 待办事项列表应用
author: guardrex
description: 逐步生成 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2020
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
ms.openlocfilehash: 106e1119db777074b5eae24f5d7e216e6127ca13
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238305"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="f1ef4-103">生成 Blazor 待办事项列表应用</span><span class="sxs-lookup"><span data-stu-id="f1ef4-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="f1ef4-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f1ef4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f1ef4-105">本教程演示如何生成和修改 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="f1ef4-106">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f1ef4-107">创建待办事项列表 Blazor 应用项目</span><span class="sxs-lookup"><span data-stu-id="f1ef4-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="f1ef4-108">修改 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="f1ef4-108">Modify Razor components</span></span>
> * <span data-ttu-id="f1ef4-109">在组件中使用事件处理和数据绑定</span><span class="sxs-lookup"><span data-stu-id="f1ef4-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="f1ef4-110">在 Blazor 应用中使用路由</span><span class="sxs-lookup"><span data-stu-id="f1ef4-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="f1ef4-111">在本教程结束时，你将拥有一个正常运行的待办事项列表应用。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f1ef4-112">必备条件</span><span class="sxs-lookup"><span data-stu-id="f1ef4-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a><span data-ttu-id="f1ef4-113">创建待办事项列表 Blazor 应用</span><span class="sxs-lookup"><span data-stu-id="f1ef4-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="f1ef4-114">在命令行界面中创建名为 `TodoList` 的新 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="f1ef4-115">上述命令将创建一个带有 `-o|--output` 选项的 `TodoList` 文件夹来保存应用。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="f1ef4-116">`TodoList` 文件夹是项目的根文件夹。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="f1ef4-117">使用以下命令将目录切换到 `TodoList` 文件夹：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="f1ef4-118">使用以下命令向应用添加一个新的 `Todo` Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="f1ef4-119">上述命令中的 `-n|--name` 指定了新的 Razor 组件的名称。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="f1ef4-120">新组件是在项目具有 `-o|--output` 选项的 `Pages` 文件夹中创建的。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="f1ef4-121">Razor 组件文件名要求首字母大写。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="f1ef4-122">打开 `Pages` 文件夹，确认 `Todo` 组件文件名以大写字母 `T` 开头。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="f1ef4-123">文件名应为 `Todo.razor`。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="f1ef4-124">在任何文件编辑器中打开 `Todo`，添加 `@page` Razor 指令添加到具有 `/todo` 的相对 URL 的文件顶部。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="f1ef4-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="f1ef4-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo0.razor?highlight=1)]

   <span data-ttu-id="f1ef4-126">保存 `Pages/Todo.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="f1ef4-127">将 `Todo` 组件添加到导航栏。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="f1ef4-128">`NavMenu` 组件用于应用的布局。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="f1ef4-129">布局是可避免应用中出现重复内容的组件。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="f1ef4-130">当应用加载组件 URL 时，`NavLink` 组件会在应用的 UI 中提供提示。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="f1ef4-131">在 `NavMenu` 组件的未排序列表 (`<ul>...</ul>`) 中，为 `Todo` 组件添加以下列表项 (`<li>...</li>`) 和 `NavLink` 组件。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="f1ef4-132">在 `Shared/NavMenu.razor`中：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="f1ef4-133">保存 `Shared/NavMenu.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="f1ef4-134">从 `TodoList` 文件夹，在命令行界面中执行 [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) 命令，以生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="f1ef4-135">应用运行后，请在应用的导航栏中选择 `Todo` 链接来访问新的 Todo 页面，该链接将在 `/todo` 处加载页面。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="f1ef4-136">让应用继续运行命令行界面。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-136">Leave the app running the command shell.</span></span> <span data-ttu-id="f1ef4-137">每次保存文件时，都将自动重新生成应用。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="f1ef4-138">在编译和重启时，浏览器会暂时断开与应用的连接。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="f1ef4-139">重新建立连接后，会自动重新加载浏览器中的页面。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="f1ef4-140">向项目的根目录添加 `TodoItem.cs` 文件（`TodoList` 文件夹），以保存一个用于表示待办项的类。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="f1ef4-141">为 `TodoItem` 类使用以下 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="f1ef4-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="f1ef4-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]
   
   > [!NOTE]
   > <span data-ttu-id="f1ef4-143">如果使用 Visual Studio 创建 `ToDoItem.cs` 文件和 `ToDoItem` 类，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-143">If using Visual Studio to create the `ToDoItem.cs` file and `ToDoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="f1ef4-144">删除 Visual Studio 为类生成的命名空间。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-144">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="f1ef4-145">使用前面代码块中的“复制”按钮，并替换 Visual Studio 生成的文件的全部内容。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-145">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="f1ef4-146">返回 `Todo` 组件并执行以下任务：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-146">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="f1ef4-147">在 `@code` 块中为待办项添加一个字段。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-147">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="f1ef4-148">`Todo` 组件使用此字段来维护待办项列表的状态。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-148">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="f1ef4-149">添加无序列表标记和 `foreach` 循环，以将每个待办项呈现为列表项 (`<li>`)。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-149">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="f1ef4-150">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="f1ef4-150">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="f1ef4-151">该应用需要 UI 元素来将待办项添加到列表。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-151">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="f1ef4-152">在未排序列表 (`<ul>...</ul>`) 下方添加一个文本输入 (`<input>`) 和一个按钮 (`<button>`)：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-152">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo3.razor?highlight=12-13)]

1. <span data-ttu-id="f1ef4-153">保存 `TodoItem.cs` 文件和更新的 `Pages/Todo.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-153">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="f1ef4-154">在命令行界面中，保存文件时，将自动重新生成应用。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-154">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="f1ef4-155">浏览器会暂时断开与该应用的连接，并在重新建立连接后重新加载页面。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-155">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="f1ef4-156">选择“`Add todo`”按钮时没有任何反应，因为没有事件处理程序连接到该按钮。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-156">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="f1ef4-157">向 `Todo` 组件添加 `AddTodo` 方法，并使用 `@onclick` 属性注册该方法来选择按钮。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-157">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="f1ef4-158">选择按钮时，会调用 `AddTodo` C# 方法：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-158">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="f1ef4-159">若要获取新的待办项的标题，请在 `@code` 块的顶部添加一个 `newTodo` 字符串：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-159">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo5.razor?highlight=3)]

   <span data-ttu-id="f1ef4-160">修改文本 `<input>` 元素，使用 `@bind` 属性来绑定 `newTodo`：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-160">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="f1ef4-161">更新 `AddTodo` 方法，将具有指定标题的 `TodoItem` 添加到列表。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-161">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="f1ef4-162">通过将 `newTodo` 设置为空字符串来清除文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-162">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo6.razor?highlight=19-26)]

1. <span data-ttu-id="f1ef4-163">保存 `Pages/Todo.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-163">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="f1ef4-164">应用程序会在命令行界面中自动重新生成。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-164">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="f1ef4-165">浏览器重新连接到应用后，页面会在浏览器中重新加载。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-165">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="f1ef4-166">每个待办项的标题文本都可以编辑，复选框可以帮助用户跟踪已完成的项。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-166">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="f1ef4-167">为每个待办项添加一个复选框输入，并将它的值绑定到 `IsDone` 属性。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-167">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="f1ef4-168">将 `@todo.Title` 更改为 `<input>` 元素，后者通过 `@bind` 绑定到 `todo.Title`：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-168">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo7.razor?highlight=4-7)]

1. <span data-ttu-id="f1ef4-169">更新 `<h3>` 标头，显示尚未完成的待办项数目（`IsDone` 是 `false`）。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-169">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="f1ef4-170">已完成的 `Todo` 组件 (`Pages/Todo.razor`)：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-170">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="f1ef4-171">保存 `Pages/Todo.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-171">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="f1ef4-172">应用程序会在命令行界面中自动重新生成。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-172">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="f1ef4-173">浏览器重新连接到应用后，页面会在浏览器中重新加载。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-173">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="f1ef4-174">添加项、编辑项，并将待办标记为“已完成”来测试组件。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-174">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="f1ef4-175">完成后，请在命令行界面中关闭应用。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-175">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="f1ef4-176">许多命令行界面接受键盘命令 <kbd>Ctrl</kbd>+<kbd>c</kbd> 来停止应用。</span><span class="sxs-lookup"><span data-stu-id="f1ef4-176">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f1ef4-177">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f1ef4-177">Next steps</span></span>

<span data-ttu-id="f1ef4-178">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f1ef4-179">创建待办事项列表 Blazor 应用项目</span><span class="sxs-lookup"><span data-stu-id="f1ef4-179">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="f1ef4-180">修改 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="f1ef4-180">Modify Razor components</span></span>
> * <span data-ttu-id="f1ef4-181">在组件中使用事件处理和数据绑定</span><span class="sxs-lookup"><span data-stu-id="f1ef4-181">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="f1ef4-182">在 Blazor 应用中使用路由</span><span class="sxs-lookup"><span data-stu-id="f1ef4-182">Use routing in a Blazor app</span></span>

<span data-ttu-id="f1ef4-183">了解用于 ASP.NET Core Blazor 的工具：</span><span class="sxs-lookup"><span data-stu-id="f1ef4-183">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
