---
title: 生成第一个 Razor 组件应用
author: guardrex
description: 逐步生成 Razor 组件应用并了解 Razor 组件基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
uid: tutorials/first-razor-components-app
ms.openlocfilehash: 0c3dd2366581d73bad44e2911602e13c6c0daf9a
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159338"
---
# <a name="build-your-first-razor-components-app"></a>生成第一个 Razor 组件应用

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

本教程演示如何使用 Razor 组件生成应用并演示 Razor 组件基本概念。 可以使用基于 Razor 组件的项目（在 .NET Core 3.0 或更高版本中受支持）或使用基于 Blazor 的项目（在 .NET Core 未来版本中受支持）来利用本教程。

若要体验使用 ASP.NET Core Razor 组件，可以执行以下操作（*建议*）：

* 按照 <xref:razor-components/get-started>中的指导创建基于 Razor 组件的项目。
* 将项目命名为 `RazorComponents`。
* 从“Razor 组件”模板创建多项目解决方案。 将 Razor 组件项目生成为 RazorComponents.App。

若要体验使用 Blazor，可以执行以下操作：

* 按照 <xref:spa/blazor/get-started>中的指导创建基于 Blazor 的项目。
* 将项目命名为 `Blazor`。
* 从 Blazor 模板创建单项目解决方案。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/)（[如何下载](xref:index#how-to-download-a-sample)）。 有关先决条件，请参阅以下主题：

## <a name="build-components"></a>生成组件

1. 浏览到每个应用的三个页面：主页、计数器和提取数据。 这些页面由“Pages”文件夹中的 Razor 文件实现：Index.cshtml、Counter.cshtml 和 FetchData.cshtml。

1. 在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。 增加网页中的计数器值通常需要编写 JavaScript，但 Razor 组件使用 C# 提供了更好的方法。

1. 检查“Counter.cshtml”文件中 Counter 组件的实现。

   *Pages/Counter.cshtml*：

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter1.cshtml)]

   使用 HTML 定义 Counter 组件的 UI。 动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。 HTML 标记和 C# 呈现逻辑在构建时转换为组件类。 生成的 .NET 类的名称与文件名匹配。

   组件类的成员在 `@functions` 块中定义。 在 `@functions` 块中，可以指定组件状态（属性、字段）和方法用于处理事件或定义其他组件逻辑。 然后，可以将这些成员用作组件呈现逻辑的一部分，并用于处理事件。

   选中“单击我”按钮时：

   * 调用 Counter 组件的已注册 `onclick` 处理程序（`IncrementCount` 方法）。
   * Counter 组件重新生成其呈现树。
   * 将新的呈现树与前一个呈现树进行比较。
   * 仅应用对文档对象模型 (DOM) 的修改。 显示的计数将会更新。

1. 修改 Counter 组件的 C# 逻辑，使计数递增 2 而不是 1。

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter2.cshtml?highlight=14)]

1. 重新生成并运行应用以查看更改。 选择“单击我”按钮，计数器的值将增加 2。

## <a name="use-components"></a>使用组件

使用类似 HTML 的语法将组件加入到另一个组件中。

1. 通过向 Index 组件添加 `<Counter />` 元素，将 Counter 组件添加到应用的 Index（主页）组件。

   如果在此体验中使用的是 Blazor，则 Survey Prompt 组件（`<SurveyPrompt>` 元素）位于 Index 组件中。 将 `<SurveyPrompt>` 元素替换为 `<Counter>` 元素。

   Pages/Index.cshtml：

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Index.cshtml?highlight=7)]

1. 重新生成并运行应用。 主页本身具有自己的计数器。

## <a name="component-parameters"></a>组件参数

组件也可以有参数。 组件参数由使用 `[Parameter]` 修饰的组件类上的专用属性定义。 使用这些属性在标记中为组件指定参数。

1. 更新组件的 `@functions` C# 代码：

   * 添加使用 `[Parameter]` 属性修饰的 `IncrementAmount` 属性。
   * 增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。

   *Pages/Counter.cshtml*：

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Counter.cshtml?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. 使用属性在 Home 组件的 `<Counter>` 元素中指定 `IncrementAmount` 参数。 将计数器递增值设置为 10。

   Pages/Index.cshtml：

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Index.cshtml?highlight=7)]

1. 重载页面。 每次选择“单击我”按钮时，主页计数器值将增加 10。 “计数器”页上的计数器值将递增 1。

## <a name="route-to-components"></a>路由到组件

“Counter.cshtml”文件顶部的 `@page` 指令指定此组件是路由终结点。 Counter 组件处理发送到 `/Counter` 的请求。 如果没有 `@page` 指令，组件将无法处理路由的请求，但仍可以被其他组件使用。

## <a name="dependency-injection"></a>依赖关系注入

通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)，组件可以使用注册了应用服务容器的服务。 使用 `@inject` 指令将服务注入到组件中。

检查 FetchData 组件的指令 (Pages/FetchData.cshtml)。 `@inject` 指令用于将 `WeatherForecastService` 服务的实例注入到组件中：

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=3)]

`WeatherForecastService` 服务注册为[单一实例](xref:fundamentals/dependency-injection#service-lifetimes)，因此整个应用中有一个服务实例。

FetchData 组件使用注入的服务（作为 `ForecastService`）来检索 `WeatherForecast` 对象的数组：

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData2.cshtml?highlight=6)]

[@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 循环用于将每个预测实例呈现为“天气”数据表中的一行：

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData3.cshtml?highlight=11-19)]

## <a name="build-a-todo-list"></a>生成待办项列表

向应用程序添加一个实现简单待办项列表的新页面。

1. 向“Pages”文件夹添加一个名为 Todo.cshtml 的空文件。

1. 为页面提供初始标记：

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. 将“待办项”页面添加到导航栏。

   NavMenu 组件 (Shared/NavMenu.csthml) 用于应用的布局。 布局是可以避免应用中出现重复内容的组件。 有关更多信息，请参见<xref:razor-components/layouts>。

   通过在“Shared/NavMenu.csthml”文件中的现有列表项下添加以下列表项标记，为“待办项”页面添加一个 `<NavLink>`：

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. 重新生成并运行应用。 访问新的“待办项”页面，确认指向“待办项”页面的链接有效。

1. 向项目的根目录添加“TodoItem.cs”文件，以保存一个用于表示待办项的类。 为 `TodoItem` 类使用以下 C# 代码：

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/TodoItem.cs)]

1. 返回到 Todo 组件 (Todo.cshtml)：

   * 在 `@functions` 块中为待办项添加字段。 Todo 组件使用此字段来维护待办项列表的状态。
   * 添加无序列表标记和 `foreach` 循环，以将每个待办项呈现为列表项。

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData4.cshtml?highlight=5-10,12-14)]

1. 该应用需要 UI 元素来将待办项添加到列表。 在列表下方添加一个文本输入和一个按钮：

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData5.cshtml?highlight=12-13)]

1. 重新生成并运行应用。 选择“添加待办项”按钮时没有任何反应，因为没有事件处理程序连接到该按钮。

1. 向 Todo 组件添加 `AddTodo` 方法，并使用 `onclick` 属性注册该方法以单击按钮：

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData6.cshtml?highlight=2,7-10)]

   选择按钮时，会调用 `AddTodo` C# 方法。

1. 要获得新待办项标题，请添加 `newTodo` 字符串字段，并使用 `bind` 属性将其绑定到文本输入的值：

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData7.cshtml?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo" />
   ```

1. 更新 `AddTodo` 方法，将具有指定标题的 `TodoItem` 添加到列表。 通过将 `newTodo` 设置为空字符串来清除文本输入的值：

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData8.cshtml?highlight=19-26)]

1. 重新生成并运行应用。 在待办项列表中添加一些待办项以测试新代码。

1. 每个待办项的标题文本都可以编辑，复选框可以帮助用户跟踪已完成的项。 为每个待办项添加一个复选框输入，并将它的值绑定到 `IsDone` 属性。 将 `@todo.Title` 更改为绑定到 `@todo.Title` 的 `<input>` 元素：

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData9.cshtml?highlight=5-6)]

1. 若要验证这些值是否已绑定，请更新 `<h1>` 标头以显示尚未完成的待办项计数（`IsDone` 是 `false`）。

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. 完成的 Todo 组件 (Todo.cshtml)：

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Todo.cshtml)]

1. 重新生成并运行应用。 添加待办项以测试新代码。

## <a name="publish-and-deploy-the-app"></a>发布和部署应用

要发布应用，请参阅<xref:host-and-deploy/razor-components/index#publish-the-app>。
