---
title: 生成首个 Blazor 应用
author: guardrex
description: 逐步生成 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- Blazor
uid: tutorials/first-blazor-app
ms.openlocfilehash: 2a83761b598a339aeaf8bec6f8fd50d09e608f0c
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681209"
---
# <a name="build-your-first-opno-locblazor-app"></a><span data-ttu-id="87585-103">生成首个 Blazor 应用</span><span class="sxs-lookup"><span data-stu-id="87585-103">Build your first Blazor app</span></span>

<span data-ttu-id="87585-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="87585-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="87585-105">本教程演示如何生成和修改 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="87585-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="87585-106">按照 <xref:blazor/get-started> 文章中的指南创建用于本教程的 Blazor 项目。</span><span class="sxs-lookup"><span data-stu-id="87585-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="87585-107">将项目命名为 ToDoList  。</span><span class="sxs-lookup"><span data-stu-id="87585-107">Name the project *ToDoList*.</span></span>

## <a name="build-components"></a><span data-ttu-id="87585-108">生成组件</span><span class="sxs-lookup"><span data-stu-id="87585-108">Build components</span></span>

1. <span data-ttu-id="87585-109">在 Pages  文件夹中浏览应用的三个页面：主页、计数器和提取数据。</span><span class="sxs-lookup"><span data-stu-id="87585-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="87585-110">这些页面由 Razor 组件文件（Index.razor  、Counter.razor  和 FetchData.razor  ）实现。</span><span class="sxs-lookup"><span data-stu-id="87585-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="87585-111">在“计数器”页上，选择“单击我”  按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="87585-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="87585-112">增加网页的计数器值通常需要编写 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="87585-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="87585-113">通过 Blazor，可以改为编写 C#。</span><span class="sxs-lookup"><span data-stu-id="87585-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="87585-114">检查 Counter.razor  文件中 `Counter` 组件的实现。</span><span class="sxs-lookup"><span data-stu-id="87585-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="87585-115">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="87585-115">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="87585-116">使用 HTML 定义 `Counter`组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="87585-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="87585-117">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="87585-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="87585-118">HTML 标记和 C# 呈现逻辑在构建时转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="87585-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="87585-119">生成的 .NET 类的名称与文件名匹配。</span><span class="sxs-lookup"><span data-stu-id="87585-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="87585-120">组件类的成员在 `@code` 块中定义。</span><span class="sxs-lookup"><span data-stu-id="87585-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="87585-121">在 `@code` 块中，可以指定组件状态（属性、字段）和方法用于处理事件或定义其他组件逻辑。</span><span class="sxs-lookup"><span data-stu-id="87585-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="87585-122">然后，可以将这些成员用作组件呈现逻辑的一部分，并用于处理事件。</span><span class="sxs-lookup"><span data-stu-id="87585-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="87585-123">选中“单击我”  按钮时：</span><span class="sxs-lookup"><span data-stu-id="87585-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="87585-124">调用 `Counter` 组件的已注册 `onclick` 处理程序（`IncrementCount` 方法）。</span><span class="sxs-lookup"><span data-stu-id="87585-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="87585-125">`Counter` 组件重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="87585-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="87585-126">将新的呈现树与前一个呈现树进行比较。</span><span class="sxs-lookup"><span data-stu-id="87585-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="87585-127">仅应用对文档对象模型 (DOM) 的修改。</span><span class="sxs-lookup"><span data-stu-id="87585-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="87585-128">显示的计数将会更新。</span><span class="sxs-lookup"><span data-stu-id="87585-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="87585-129">修改 `Counter` 组件的 C# 逻辑，使计数递增 2 而不是 1。</span><span class="sxs-lookup"><span data-stu-id="87585-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="87585-130">重新生成并运行应用以查看更改。</span><span class="sxs-lookup"><span data-stu-id="87585-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="87585-131">选择“单击我”  按钮。</span><span class="sxs-lookup"><span data-stu-id="87585-131">Select the **Click me** button.</span></span> <span data-ttu-id="87585-132">计数器的值将增加 2。</span><span class="sxs-lookup"><span data-stu-id="87585-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="87585-133">使用组件</span><span class="sxs-lookup"><span data-stu-id="87585-133">Use components</span></span>

<span data-ttu-id="87585-134">使用 HTML 语法将组件加入到另一个组件中。</span><span class="sxs-lookup"><span data-stu-id="87585-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="87585-135">通过向 `Index` 组件 (Index.razor  ) 添加 `<Counter />` 元素，将 `Counter` 组件添加到应用的 `Index` 组件。</span><span class="sxs-lookup"><span data-stu-id="87585-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="87585-136">如果在此体验中使用的是 Blazor WebAssembly，则 `Index` 组件使用 `SurveyPrompt` 组件。</span><span class="sxs-lookup"><span data-stu-id="87585-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="87585-137">将 `<SurveyPrompt>` 元素替换为 `<Counter />` 元素。</span><span class="sxs-lookup"><span data-stu-id="87585-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="87585-138">如果在此体验中使用的是 Blazor Server 应用，请向 `Index` 组件添加 `<Counter />` 元素：</span><span class="sxs-lookup"><span data-stu-id="87585-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="87585-139">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="87585-139">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="87585-140">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="87585-140">Rebuild and run the app.</span></span> <span data-ttu-id="87585-141">`Index` 组件有其自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="87585-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="87585-142">组件参数</span><span class="sxs-lookup"><span data-stu-id="87585-142">Component parameters</span></span>

<span data-ttu-id="87585-143">组件也可以有参数。</span><span class="sxs-lookup"><span data-stu-id="87585-143">Components can also have parameters.</span></span> <span data-ttu-id="87585-144">组件参数由具有 `[Parameter]` 的组件类上的公共属性定义。</span><span class="sxs-lookup"><span data-stu-id="87585-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="87585-145">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="87585-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="87585-146">更新组件的 `@code` C# 代码：</span><span class="sxs-lookup"><span data-stu-id="87585-146">Update the component's `@code` C# code:</span></span>

   * <span data-ttu-id="87585-147">使用 `[Parameter]` 特性添加公共 `IncrementAmount` 属性。</span><span class="sxs-lookup"><span data-stu-id="87585-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="87585-148">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="87585-148">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="87585-149">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="87585-149">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="87585-150">使用属性在 `Index` 组件的 `<Counter>` 元素中指定 `IncrementAmount` 参数。</span><span class="sxs-lookup"><span data-stu-id="87585-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="87585-151">将计数器递增值设置为 10。</span><span class="sxs-lookup"><span data-stu-id="87585-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="87585-152">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="87585-152">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="87585-153">重新加载 `Index` 组件。</span><span class="sxs-lookup"><span data-stu-id="87585-153">Reload the `Index` component.</span></span> <span data-ttu-id="87585-154">每次选择“单击我”  按钮时，计数器值递增 10。</span><span class="sxs-lookup"><span data-stu-id="87585-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="87585-155">`Counter` 组件中的计数器继续递增 1。</span><span class="sxs-lookup"><span data-stu-id="87585-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="87585-156">路由到组件</span><span class="sxs-lookup"><span data-stu-id="87585-156">Route to components</span></span>

<span data-ttu-id="87585-157">Counter.razor  文件顶部的 `@page` 指令指定 `Counter` 组件是路由终结点。</span><span class="sxs-lookup"><span data-stu-id="87585-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="87585-158">`Counter` 组件处理发送到 `/counter` 的请求。</span><span class="sxs-lookup"><span data-stu-id="87585-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="87585-159">如果没有 `@page` 指令，组件将无法处理路由的请求，但该组件仍可以被其他组件使用。</span><span class="sxs-lookup"><span data-stu-id="87585-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="87585-160">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="87585-160">Dependency injection</span></span>

### <a name="opno-locblazor-server-experience"></a>Blazor<span data-ttu-id="87585-161"> Server 体验</span><span class="sxs-lookup"><span data-stu-id="87585-161"> Server experience</span></span>

<span data-ttu-id="87585-162">如果使用的是 Blazor Server 应用，则 `WeatherForecastService` 服务在 `Startup.ConfigureServices` 中注册为[单一实例](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="87585-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="87585-163">可通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 在整个应用中使用服务的实例：</span><span class="sxs-lookup"><span data-stu-id="87585-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="87585-164">`@inject` 指令用于将 `WeatherForecastService` 服务的实例注入到 `FetchData` 组件中。</span><span class="sxs-lookup"><span data-stu-id="87585-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="87585-165">*Pages/FetchData.razor*：</span><span class="sxs-lookup"><span data-stu-id="87585-165">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="87585-166">`FetchData` 组件使用注入的服务（作为 `ForecastService`）来检索 `WeatherForecast` 对象的数组：</span><span class="sxs-lookup"><span data-stu-id="87585-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>Blazor<span data-ttu-id="87585-167"> WebAssembly 体验</span><span class="sxs-lookup"><span data-stu-id="87585-167"> WebAssembly experience</span></span>

<span data-ttu-id="87585-168">如果使用的是 Blazor WebAssembly 应用，则注入了 `HttpClient`，以从 wwwroot/sample-data 文件夹的 weather.json 文件中获取天气预测数据。  </span><span class="sxs-lookup"><span data-stu-id="87585-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="87585-169">*Pages/FetchData.razor*：</span><span class="sxs-lookup"><span data-stu-id="87585-169">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="87585-170">[@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 循环用于将每个预测实例呈现为“天气”数据表中的一行：</span><span class="sxs-lookup"><span data-stu-id="87585-170">An [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="87585-171">生成待办项列表</span><span class="sxs-lookup"><span data-stu-id="87585-171">Build a todo list</span></span>

<span data-ttu-id="87585-172">向应用添加一个实现简单待办事项列表的新组件。</span><span class="sxs-lookup"><span data-stu-id="87585-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="87585-173">向 Pages  文件夹中的应用添加一个名为 Todo.razor  的空文件：</span><span class="sxs-lookup"><span data-stu-id="87585-173">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="87585-174">为组件提供初始标记：</span><span class="sxs-lookup"><span data-stu-id="87585-174">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="87585-175">将 `Todo` 组件添加到导航栏。</span><span class="sxs-lookup"><span data-stu-id="87585-175">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="87585-176">`NavMenu` 组件 (Shared/NavMenu.razor  ) 用于应用的布局。</span><span class="sxs-lookup"><span data-stu-id="87585-176">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="87585-177">布局是可以避免应用中出现重复内容的组件。</span><span class="sxs-lookup"><span data-stu-id="87585-177">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="87585-178">通过在“Shared/NavMenu.razor”  文件中的现有列表项下添加以下列表项标记，为 `Todo` 组件添加一个 `<NavLink>` 元素：</span><span class="sxs-lookup"><span data-stu-id="87585-178">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="87585-179">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="87585-179">Rebuild and run the app.</span></span> <span data-ttu-id="87585-180">访问新的“待办事项”页面，确认指向 `Todo` 组件的链接有效。</span><span class="sxs-lookup"><span data-stu-id="87585-180">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="87585-181">向项目的根目录添加“TodoItem.cs”  文件，以保存一个用于表示待办项的类。</span><span class="sxs-lookup"><span data-stu-id="87585-181">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="87585-182">为 `TodoItem` 类使用以下 C# 代码：</span><span class="sxs-lookup"><span data-stu-id="87585-182">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="87585-183">返回到 `Todo` 组件 (Pages/Todo.razor  )：</span><span class="sxs-lookup"><span data-stu-id="87585-183">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="87585-184">在 `@code` 块中为待办项添加一个字段。</span><span class="sxs-lookup"><span data-stu-id="87585-184">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="87585-185">`Todo` 组件使用此字段来维护待办项列表的状态。</span><span class="sxs-lookup"><span data-stu-id="87585-185">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="87585-186">添加无序列表标记和 `foreach` 循环，以将每个待办项呈现为列表项 (`<li>`)。</span><span class="sxs-lookup"><span data-stu-id="87585-186">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="87585-187">该应用需要 UI 元素来将待办项添加到列表。</span><span class="sxs-lookup"><span data-stu-id="87585-187">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="87585-188">在未排序列表 (`<ul>...</ul>`) 下方添加一个文本输入 (`<input>`) 和一个按钮 (`<button>`)：</span><span class="sxs-lookup"><span data-stu-id="87585-188">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="87585-189">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="87585-189">Rebuild and run the app.</span></span> <span data-ttu-id="87585-190">选择“添加待办项”  按钮时没有任何反应，因为没有事件处理程序连接到该按钮。</span><span class="sxs-lookup"><span data-stu-id="87585-190">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="87585-191">向 `Todo` 组件添加 `AddTodo` 方法，并使用 `@onclick` 属性注册该方法以选择按钮。</span><span class="sxs-lookup"><span data-stu-id="87585-191">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="87585-192">选择按钮时，会调用 `AddTodo` C# 方法：</span><span class="sxs-lookup"><span data-stu-id="87585-192">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="87585-193">要获得新待办项标题，请在 `@code` 块顶部添加 `newTodo` 字符串字段，并使用 `<input>` 元素中的 `bind` 属性将其绑定到文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="87585-193">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="87585-194">更新 `AddTodo` 方法，将具有指定标题的 `TodoItem` 添加到列表。</span><span class="sxs-lookup"><span data-stu-id="87585-194">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="87585-195">通过将 `newTodo` 设置为空字符串来清除文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="87585-195">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="87585-196">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="87585-196">Rebuild and run the app.</span></span> <span data-ttu-id="87585-197">在待办项列表中添加一些待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="87585-197">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="87585-198">每个待办项的标题文本都可以编辑，复选框可以帮助用户跟踪已完成的项。</span><span class="sxs-lookup"><span data-stu-id="87585-198">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="87585-199">为每个待办项添加一个复选框输入，并将它的值绑定到 `IsDone` 属性。</span><span class="sxs-lookup"><span data-stu-id="87585-199">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="87585-200">将 `@todo.Title` 更改为绑定到 `@todo.Title` 的 `<input>` 元素：</span><span class="sxs-lookup"><span data-stu-id="87585-200">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="87585-201">若要验证这些值是否已绑定，请更新 `<h1>` 标头以显示尚未完成的待办项计数（`IsDone` 是 `false`）。</span><span class="sxs-lookup"><span data-stu-id="87585-201">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="87585-202">完成的 `Todo` 组件 (Pages/Todo.razor  )：</span><span class="sxs-lookup"><span data-stu-id="87585-202">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="87585-203">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="87585-203">Rebuild and run the app.</span></span> <span data-ttu-id="87585-204">添加待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="87585-204">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
