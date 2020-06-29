---
title: 生成首个 Blazor 应用
author: guardrex
description: 逐步生成 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 892663a533a207df84b0fce9af259a7dc212bc9b
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292771"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="b9f74-103">生成首个 Blazor 应用</span><span class="sxs-lookup"><span data-stu-id="b9f74-103">Build your first Blazor app</span></span>

<span data-ttu-id="b9f74-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b9f74-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b9f74-105">本教程演示如何生成和修改 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="b9f74-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="b9f74-106">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="b9f74-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b9f74-107">创建待办事项列表 Blazor 应用项目</span><span class="sxs-lookup"><span data-stu-id="b9f74-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="b9f74-108">修改 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="b9f74-108">Modify Razor components</span></span>
> * <span data-ttu-id="b9f74-109">在组件中使用事件处理和数据绑定</span><span class="sxs-lookup"><span data-stu-id="b9f74-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="b9f74-110">在 Blazor 应用中使用依赖关系注入 (DI) 和路由</span><span class="sxs-lookup"><span data-stu-id="b9f74-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="b9f74-111">在本教程结束时，你将拥有一个正常运行的待办事项列表应用。</span><span class="sxs-lookup"><span data-stu-id="b9f74-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="b9f74-112">生成组件</span><span class="sxs-lookup"><span data-stu-id="b9f74-112">Build components</span></span>

1. <span data-ttu-id="b9f74-113">按照 <xref:blazor/get-started> 文章中的指南创建用于本教程的 Blazor 项目。</span><span class="sxs-lookup"><span data-stu-id="b9f74-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="b9f74-114">将项目命名为 `ToDoList`。</span><span class="sxs-lookup"><span data-stu-id="b9f74-114">Name the project `ToDoList`.</span></span>

1. <span data-ttu-id="b9f74-115">在 `Pages` 文件夹中浏览应用的三个页面：`Home`、`Counter` 和 `Fetch data`。</span><span class="sxs-lookup"><span data-stu-id="b9f74-115">Browse to each of the app's three pages in the `Pages` folder: `Home`, `Counter`, and `Fetch data`.</span></span> <span data-ttu-id="b9f74-116">这些页面由 Razor 组件文件 `Index.razor`、`Counter.razor` 和 `FetchData.razor` 实现。</span><span class="sxs-lookup"><span data-stu-id="b9f74-116">These pages are implemented by the Razor component files `Index.razor`, `Counter.razor`, and `FetchData.razor`.</span></span>

1. <span data-ttu-id="b9f74-117">在“`Counter`”页上，选择相关按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="b9f74-117">On the `Counter` page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="b9f74-118">增加网页的计数器值通常需要编写 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="b9f74-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="b9f74-119">通过 Blazor，可以改为编写 C#。</span><span class="sxs-lookup"><span data-stu-id="b9f74-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="b9f74-120">检查 `Counter.razor` 文件中 `Counter` 组件的实现。</span><span class="sxs-lookup"><span data-stu-id="b9f74-120">Examine the implementation of the `Counter` component in the `Counter.razor` file.</span></span>

   <span data-ttu-id="b9f74-121">`Pages/Counter.razor`：</span><span class="sxs-lookup"><span data-stu-id="b9f74-121">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="b9f74-122">使用 HTML 定义 `Counter`组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="b9f74-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="b9f74-123">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="b9f74-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="b9f74-124">HTML 标记和 C# 呈现逻辑在构建时转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="b9f74-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="b9f74-125">生成的 .NET 类的名称与文件名匹配。</span><span class="sxs-lookup"><span data-stu-id="b9f74-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="b9f74-126">组件类的成员在 `@code` 块中定义。</span><span class="sxs-lookup"><span data-stu-id="b9f74-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="b9f74-127">在 `@code` 块中，可以指定组件状态（属性、字段）和方法用于处理事件或定义其他组件逻辑。</span><span class="sxs-lookup"><span data-stu-id="b9f74-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="b9f74-128">然后，可以将这些成员用作组件呈现逻辑的一部分，并用于处理事件。</span><span class="sxs-lookup"><span data-stu-id="b9f74-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="b9f74-129">选择计数器递增按钮后：</span><span class="sxs-lookup"><span data-stu-id="b9f74-129">When the counter increment button is selected:</span></span>

   * <span data-ttu-id="b9f74-130">调用 `Counter` 组件的已注册 `onclick` 处理程序（`IncrementCount` 方法）。</span><span class="sxs-lookup"><span data-stu-id="b9f74-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="b9f74-131">`Counter` 组件重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="b9f74-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="b9f74-132">将新的呈现树与前一个呈现树进行比较。</span><span class="sxs-lookup"><span data-stu-id="b9f74-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="b9f74-133">仅应用对文档对象模型 (DOM) 的修改。</span><span class="sxs-lookup"><span data-stu-id="b9f74-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="b9f74-134">显示的计数将会更新。</span><span class="sxs-lookup"><span data-stu-id="b9f74-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="b9f74-135">修改 `Counter` 组件的 C# 逻辑，使计数递增 2 而不是 1。</span><span class="sxs-lookup"><span data-stu-id="b9f74-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="b9f74-136">重新生成并运行应用以查看更改。</span><span class="sxs-lookup"><span data-stu-id="b9f74-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="b9f74-137">选择该按钮。</span><span class="sxs-lookup"><span data-stu-id="b9f74-137">Select the button.</span></span> <span data-ttu-id="b9f74-138">计数器的值将增加 2。</span><span class="sxs-lookup"><span data-stu-id="b9f74-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="b9f74-139">使用组件</span><span class="sxs-lookup"><span data-stu-id="b9f74-139">Use components</span></span>

<span data-ttu-id="b9f74-140">使用 HTML 语法将组件加入到另一个组件中。</span><span class="sxs-lookup"><span data-stu-id="b9f74-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="b9f74-141">通过向 `Index` 组件 (`Index.razor`) 添加 `<Counter />` 元素，将 `Index` 组件添加到应用的 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="b9f74-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (`Index.razor`).</span></span>

   <span data-ttu-id="b9f74-142">如果在此体验中使用的是 Blazor WebAssembly，则 `Index` 组件使用 `SurveyPrompt` 组件。</span><span class="sxs-lookup"><span data-stu-id="b9f74-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="b9f74-143">将 `<SurveyPrompt>` 元素替换为 `<Counter />` 元素。</span><span class="sxs-lookup"><span data-stu-id="b9f74-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="b9f74-144">如果在此体验中使用的是 Blazor Server 应用，请向 `Index` 组件添加 `<Counter />` 元素：</span><span class="sxs-lookup"><span data-stu-id="b9f74-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="b9f74-145">`Pages/Index.razor`：</span><span class="sxs-lookup"><span data-stu-id="b9f74-145">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="b9f74-146">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="b9f74-146">Rebuild and run the app.</span></span> <span data-ttu-id="b9f74-147">`Index` 组件有其自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="b9f74-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="b9f74-148">组件参数</span><span class="sxs-lookup"><span data-stu-id="b9f74-148">Component parameters</span></span>

<span data-ttu-id="b9f74-149">组件也可以有参数。</span><span class="sxs-lookup"><span data-stu-id="b9f74-149">Components can also have parameters.</span></span> <span data-ttu-id="b9f74-150">组件参数是使用组件类中包含 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 特性的公共属性定义的。</span><span class="sxs-lookup"><span data-stu-id="b9f74-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="b9f74-151">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="b9f74-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="b9f74-152">更新组件的 `@code` C# 代码，如下所示：</span><span class="sxs-lookup"><span data-stu-id="b9f74-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="b9f74-153">添加包含 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 特性的公共 `IncrementAmount` 属性。</span><span class="sxs-lookup"><span data-stu-id="b9f74-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="b9f74-154">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount` 属性。</span><span class="sxs-lookup"><span data-stu-id="b9f74-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="b9f74-155">`Pages/Counter.razor`：</span><span class="sxs-lookup"><span data-stu-id="b9f74-155">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="b9f74-156">使用属性在 `Index` 组件的 `<Counter>` 元素中指定 `IncrementAmount` 参数。</span><span class="sxs-lookup"><span data-stu-id="b9f74-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="b9f74-157">将计数器递增值设置为 10。</span><span class="sxs-lookup"><span data-stu-id="b9f74-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="b9f74-158">`Pages/Index.razor`：</span><span class="sxs-lookup"><span data-stu-id="b9f74-158">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="b9f74-159">重新加载 `Index` 组件。</span><span class="sxs-lookup"><span data-stu-id="b9f74-159">Reload the `Index` component.</span></span> <span data-ttu-id="b9f74-160">每次选择该按钮时，计数器值递增 10。</span><span class="sxs-lookup"><span data-stu-id="b9f74-160">The counter increments by ten each time the button is selected.</span></span> <span data-ttu-id="b9f74-161">`Counter` 组件中的计数器继续递增 1。</span><span class="sxs-lookup"><span data-stu-id="b9f74-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="b9f74-162">路由到组件</span><span class="sxs-lookup"><span data-stu-id="b9f74-162">Route to components</span></span>

<span data-ttu-id="b9f74-163">`Counter.razor` 文件顶部的 `@page` 指令指定 `Counter` 组件是路由终结点。</span><span class="sxs-lookup"><span data-stu-id="b9f74-163">The `@page` directive at the top of the `Counter.razor` file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="b9f74-164">`Counter` 组件处理发送到 `/counter` 的请求。</span><span class="sxs-lookup"><span data-stu-id="b9f74-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="b9f74-165">如果没有 `@page` 指令，组件将无法处理路由的请求，但该组件仍可以被其他组件使用。</span><span class="sxs-lookup"><span data-stu-id="b9f74-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="b9f74-166">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="b9f74-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="b9f74-167"> Server 体验</span><span class="sxs-lookup"><span data-stu-id="b9f74-167"> Server experience</span></span>

<span data-ttu-id="b9f74-168">如果使用的是 Blazor Server 应用，则 `WeatherForecastService` 服务在 `Startup.ConfigureServices` 中注册为[单一实例](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="b9f74-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b9f74-169">可通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 在整个应用中使用服务的实例：</span><span class="sxs-lookup"><span data-stu-id="b9f74-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="b9f74-170">[`@inject`](xref:mvc/views/razor#inject) 指令用于将 `WeatherForecastService` 服务的实例注入到 `FetchData` 组件中。</span><span class="sxs-lookup"><span data-stu-id="b9f74-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="b9f74-171">`Pages/FetchData.razor`：</span><span class="sxs-lookup"><span data-stu-id="b9f74-171">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="b9f74-172">`FetchData` 组件使用注入的服务（作为 `ForecastService`）来检索 `WeatherForecast` 对象的数组：</span><span class="sxs-lookup"><span data-stu-id="b9f74-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="b9f74-173"> WebAssembly 体验</span><span class="sxs-lookup"><span data-stu-id="b9f74-173"> WebAssembly experience</span></span>

<span data-ttu-id="b9f74-174">如果使用的是 Blazor WebAssembly 应用，则注入了 <xref:System.Net.Http.HttpClient>，以从 `wwwroot/sample-data` 文件夹的 `weather.json` 文件中获取天气预测数据。</span><span class="sxs-lookup"><span data-stu-id="b9f74-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the `weather.json` file in the `wwwroot/sample-data` folder.</span></span>

<span data-ttu-id="b9f74-175">`Pages/FetchData.razor`：</span><span class="sxs-lookup"><span data-stu-id="b9f74-175">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="b9f74-176">[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环用于将每个预测实例呈现为“天气”数据表中的一行：</span><span class="sxs-lookup"><span data-stu-id="b9f74-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="b9f74-177">生成待办项列表</span><span class="sxs-lookup"><span data-stu-id="b9f74-177">Build a todo list</span></span>

<span data-ttu-id="b9f74-178">向应用添加一个实现简单待办事项列表的新组件。</span><span class="sxs-lookup"><span data-stu-id="b9f74-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="b9f74-179">向 `Pages` 文件夹中的应用添加一个新的 `Todo` Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="b9f74-179">Add a new `Todo` Razor component to the app in the `Pages` folder.</span></span> <span data-ttu-id="b9f74-180">如果你使用的是 Visual Studio，请右键单击 `Pages` 文件夹，然后选择“添加” > “新项目” > “Razor 组件”  。</span><span class="sxs-lookup"><span data-stu-id="b9f74-180">If you're using Visual Studio, right-click the `Pages` folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="b9f74-181">将组件的文件命名为 `Todo.razor`。</span><span class="sxs-lookup"><span data-stu-id="b9f74-181">Name the component's file `Todo.razor`.</span></span> <span data-ttu-id="b9f74-182">在其他开发环境中，将空白文件添加到名为 `Todo.razor` 的 `Pages` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="b9f74-182">In other development environments, add a blank file to the `Pages` folder named `Todo.razor`.</span></span> Razor<span data-ttu-id="b9f74-183"> 组件文件名要求首字母大写，因此请确认 `Todo` 组件的文件名以大写字母 `T` 开头。</span><span class="sxs-lookup"><span data-stu-id="b9f74-183"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="b9f74-184">为组件提供初始标记：</span><span class="sxs-lookup"><span data-stu-id="b9f74-184">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="b9f74-185">将 `Todo` 组件添加到导航栏。</span><span class="sxs-lookup"><span data-stu-id="b9f74-185">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="b9f74-186">`NavMenu` 组件 (`Shared/NavMenu.razor`) 用于应用的布局。</span><span class="sxs-lookup"><span data-stu-id="b9f74-186">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="b9f74-187">布局是可以避免应用中出现重复内容的组件。</span><span class="sxs-lookup"><span data-stu-id="b9f74-187">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="b9f74-188">通过在 `Shared/NavMenu.razor` 文件中的现有列表项下添加以下列表项标记，为 `Todo` 组件添加一个 `<NavLink>` 元素：</span><span class="sxs-lookup"><span data-stu-id="b9f74-188">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="b9f74-189">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="b9f74-189">Rebuild and run the app.</span></span> <span data-ttu-id="b9f74-190">访问新的“待办事项”页面，确认指向 `Todo` 组件的链接有效。</span><span class="sxs-lookup"><span data-stu-id="b9f74-190">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="b9f74-191">向项目的根目录添加 `TodoItem.cs` 文件，以保存一个用于表示待办项的类。</span><span class="sxs-lookup"><span data-stu-id="b9f74-191">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="b9f74-192">为 `TodoItem` 类使用以下 C# 代码：</span><span class="sxs-lookup"><span data-stu-id="b9f74-192">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="b9f74-193">返回到 `Todo` 组件 (`Pages/Todo.razor`)：</span><span class="sxs-lookup"><span data-stu-id="b9f74-193">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="b9f74-194">在 `@code` 块中为待办项添加一个字段。</span><span class="sxs-lookup"><span data-stu-id="b9f74-194">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="b9f74-195">`Todo` 组件使用此字段来维护待办项列表的状态。</span><span class="sxs-lookup"><span data-stu-id="b9f74-195">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="b9f74-196">添加无序列表标记和 `foreach` 循环，以将每个待办项呈现为列表项 (`<li>`)。</span><span class="sxs-lookup"><span data-stu-id="b9f74-196">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="b9f74-197">该应用需要 UI 元素来将待办项添加到列表。</span><span class="sxs-lookup"><span data-stu-id="b9f74-197">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="b9f74-198">在未排序列表 (`<ul>...</ul>`) 下方添加一个文本输入 (`<input>`) 和一个按钮 (`<button>`)：</span><span class="sxs-lookup"><span data-stu-id="b9f74-198">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="b9f74-199">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="b9f74-199">Rebuild and run the app.</span></span> <span data-ttu-id="b9f74-200">选择“`Add todo`”按钮时没有任何反应，因为没有事件处理程序连接到该按钮。</span><span class="sxs-lookup"><span data-stu-id="b9f74-200">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="b9f74-201">向 `Todo` 组件添加 `AddTodo` 方法，并使用 `@onclick` 属性注册该方法以选择按钮。</span><span class="sxs-lookup"><span data-stu-id="b9f74-201">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="b9f74-202">选择按钮时，会调用 `AddTodo` C# 方法：</span><span class="sxs-lookup"><span data-stu-id="b9f74-202">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="b9f74-203">要获得新待办项标题，请在 `@code` 块顶部添加 `newTodo` 字符串字段，并使用 `<input>` 元素中的 `bind` 属性将其绑定到文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="b9f74-203">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="b9f74-204">更新 `AddTodo` 方法，将具有指定标题的 `TodoItem` 添加到列表。</span><span class="sxs-lookup"><span data-stu-id="b9f74-204">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="b9f74-205">通过将 `newTodo` 设置为空字符串来清除文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="b9f74-205">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="b9f74-206">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="b9f74-206">Rebuild and run the app.</span></span> <span data-ttu-id="b9f74-207">在待办项列表中添加一些待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="b9f74-207">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="b9f74-208">每个待办项的标题文本都可以编辑，复选框可以帮助用户跟踪已完成的项。</span><span class="sxs-lookup"><span data-stu-id="b9f74-208">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="b9f74-209">为每个待办项添加一个复选框输入，并将它的值绑定到 `IsDone` 属性。</span><span class="sxs-lookup"><span data-stu-id="b9f74-209">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="b9f74-210">将 `@todo.Title` 更改为绑定到 `@todo.Title` 的 `<input>` 元素：</span><span class="sxs-lookup"><span data-stu-id="b9f74-210">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="b9f74-211">若要验证这些值是否已绑定，请更新 `<h3>` 标头以显示尚未完成的待办项计数（`IsDone` 是 `false`）。</span><span class="sxs-lookup"><span data-stu-id="b9f74-211">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="b9f74-212">已完成的 `Todo` 组件 (`Pages/Todo.razor`)：</span><span class="sxs-lookup"><span data-stu-id="b9f74-212">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="b9f74-213">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="b9f74-213">Rebuild and run the app.</span></span> <span data-ttu-id="b9f74-214">添加待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="b9f74-214">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b9f74-215">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b9f74-215">Next steps</span></span>

<span data-ttu-id="b9f74-216">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="b9f74-216">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b9f74-217">创建待办事项列表 Blazor 应用项目</span><span class="sxs-lookup"><span data-stu-id="b9f74-217">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="b9f74-218">修改 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="b9f74-218">Modify Razor components</span></span>
> * <span data-ttu-id="b9f74-219">在组件中使用事件处理和数据绑定</span><span class="sxs-lookup"><span data-stu-id="b9f74-219">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="b9f74-220">在 Blazor 应用中使用依赖关系注入 (DI) 和路由</span><span class="sxs-lookup"><span data-stu-id="b9f74-220">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="b9f74-221">了解如何生成和使用组件：</span><span class="sxs-lookup"><span data-stu-id="b9f74-221">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
