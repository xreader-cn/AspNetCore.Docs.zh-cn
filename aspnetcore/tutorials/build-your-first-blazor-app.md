---
title: 生成你的第一个 Blazor 应用
author: guardrex
description: 逐步生成 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 310eb211f68076d6f52d6427940e07736d833e5b
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614631"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="5ea27-103">生成你的第一个 Blazor 应用</span><span class="sxs-lookup"><span data-stu-id="5ea27-103">Build your first Blazor app</span></span>

<span data-ttu-id="5ea27-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5ea27-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="5ea27-105">本教程将演示如何使用服务器端 Razor 组件或客户端 Blazor 生成应用。</span><span class="sxs-lookup"><span data-stu-id="5ea27-105">This tutorial shows you how to build an app with server-side Razor Components or client-side Blazor.</span></span>

<span data-ttu-id="5ea27-106">若要体验服务器端 ASP.NET Core Razor 组件，可以执行以下操作（建议）：</span><span class="sxs-lookup"><span data-stu-id="5ea27-106">For an experience using server-side ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="5ea27-107">按照 <xref:blazor/get-started#server-side-razor-components-experience> 中的《Razor 组件体验》指南创建基于 Razor 组件的项目。</span><span class="sxs-lookup"><span data-stu-id="5ea27-107">Follow the *Razor Components experience* guidance in <xref:blazor/get-started#server-side-razor-components-experience> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="5ea27-108">将项目命名为 `RazorComponents`。</span><span class="sxs-lookup"><span data-stu-id="5ea27-108">Name the project `RazorComponents`.</span></span>

<span data-ttu-id="5ea27-109">若要体验使用 Blazor，可以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5ea27-109">For an experience using Blazor:</span></span>

* <span data-ttu-id="5ea27-110">按照 <xref:blazor/get-started#client-side-blazor-experience> 中的《Blazor 体验》指南创建基于 Blazor 的项目。</span><span class="sxs-lookup"><span data-stu-id="5ea27-110">Follow the *Blazor experience* guidance in <xref:blazor/get-started#client-side-blazor-experience> to create a Blazor-based project.</span></span>
* <span data-ttu-id="5ea27-111">将项目命名为 `Blazor`。</span><span class="sxs-lookup"><span data-stu-id="5ea27-111">Name the project `Blazor`.</span></span>

<span data-ttu-id="5ea27-112">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="5ea27-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="5ea27-113">有关先决条件，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="5ea27-113">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="5ea27-114">生成组件</span><span class="sxs-lookup"><span data-stu-id="5ea27-114">Build components</span></span>

1. <span data-ttu-id="5ea27-115">在 Components/Pages 文件夹中浏览应用的三个页面（Blazor 中的 Pages）：主页、计数器和提取数据。</span><span class="sxs-lookup"><span data-stu-id="5ea27-115">Browse to each of the app's three pages in the *Components/Pages* folder (*Pages* in Blazor): Home, Counter, and Fetch data.</span></span> <span data-ttu-id="5ea27-116">这些页面由 Razor 组件文件实现：Index.razor、Counter.razor 和FetchData.razor。</span><span class="sxs-lookup"><span data-stu-id="5ea27-116">These pages are implemented by Razor Component files: *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span> <span data-ttu-id="5ea27-117">（Blazor 将继续使用 .cshtml 文件扩展名：Index.cshtml、Counter.cshtml 和 FetchData.cshtml。）</span><span class="sxs-lookup"><span data-stu-id="5ea27-117">(Blazor continues to use the *.cshtml* file extension: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.)</span></span>

1. <span data-ttu-id="5ea27-118">在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="5ea27-118">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="5ea27-119">增加网页中的计数器值通常需要编写 JavaScript，但 Blazor 使用 C# 提供了更好的方法。</span><span class="sxs-lookup"><span data-stu-id="5ea27-119">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="5ea27-120">检查 Counter.razor 文件中 Counter 组件的实现。</span><span class="sxs-lookup"><span data-stu-id="5ea27-120">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="5ea27-121">Components/Pages/Counter.razor（Blazor 中的 Pages/Counter.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="5ea27-121">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="5ea27-122">使用 HTML 定义 Counter 组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="5ea27-122">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="5ea27-123">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="5ea27-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="5ea27-124">HTML 标记和 C# 呈现逻辑在构建时转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="5ea27-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="5ea27-125">生成的 .NET 类的名称与文件名匹配。</span><span class="sxs-lookup"><span data-stu-id="5ea27-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="5ea27-126">组件类的成员在 `@functions` 块中定义。</span><span class="sxs-lookup"><span data-stu-id="5ea27-126">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="5ea27-127">在 `@functions` 块中，可以指定组件状态（属性、字段）和方法用于处理事件或定义其他组件逻辑。</span><span class="sxs-lookup"><span data-stu-id="5ea27-127">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="5ea27-128">然后，可以将这些成员用作组件呈现逻辑的一部分，并用于处理事件。</span><span class="sxs-lookup"><span data-stu-id="5ea27-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="5ea27-129">选中“单击我”按钮时：</span><span class="sxs-lookup"><span data-stu-id="5ea27-129">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="5ea27-130">调用 Counter 组件的已注册 `onclick` 处理程序（`IncrementCount` 方法）。</span><span class="sxs-lookup"><span data-stu-id="5ea27-130">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="5ea27-131">Counter 组件重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="5ea27-131">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="5ea27-132">将新的呈现树与前一个呈现树进行比较。</span><span class="sxs-lookup"><span data-stu-id="5ea27-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="5ea27-133">仅应用对文档对象模型 (DOM) 的修改。</span><span class="sxs-lookup"><span data-stu-id="5ea27-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="5ea27-134">显示的计数将会更新。</span><span class="sxs-lookup"><span data-stu-id="5ea27-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="5ea27-135">修改 Counter 组件的 C# 逻辑，使计数递增 2 而不是 1。</span><span class="sxs-lookup"><span data-stu-id="5ea27-135">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="5ea27-136">重新生成并运行应用以查看更改。</span><span class="sxs-lookup"><span data-stu-id="5ea27-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="5ea27-137">选择“单击我”按钮，计数器的值将增加 2。</span><span class="sxs-lookup"><span data-stu-id="5ea27-137">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="5ea27-138">使用组件</span><span class="sxs-lookup"><span data-stu-id="5ea27-138">Use components</span></span>

<span data-ttu-id="5ea27-139">使用类似 HTML 的语法将组件加入到另一个组件中。</span><span class="sxs-lookup"><span data-stu-id="5ea27-139">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="5ea27-140">通过向 Index 组件添加 `<Counter />` 元素，将 Counter 组件添加到应用的 Index（主页）组件。</span><span class="sxs-lookup"><span data-stu-id="5ea27-140">Add the Counter component to the app's Index (Home) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="5ea27-141">如果在此体验中使用的是 Blazor，则 Survey Prompt 组件（`<SurveyPrompt>` 元素）位于 Index 组件中。</span><span class="sxs-lookup"><span data-stu-id="5ea27-141">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="5ea27-142">将 `<SurveyPrompt>` 元素替换为 `<Counter>` 元素。</span><span class="sxs-lookup"><span data-stu-id="5ea27-142">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="5ea27-143">Components/Pages/Index.razor（Blazor 中的 Pages/Index.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="5ea27-143">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index.razor?highlight=7)]

1. <span data-ttu-id="5ea27-144">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="5ea27-144">Rebuild and run the app.</span></span> <span data-ttu-id="5ea27-145">主页有其自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="5ea27-145">The Home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="5ea27-146">组件参数</span><span class="sxs-lookup"><span data-stu-id="5ea27-146">Component parameters</span></span>

<span data-ttu-id="5ea27-147">组件也可以有参数。</span><span class="sxs-lookup"><span data-stu-id="5ea27-147">Components can also have parameters.</span></span> <span data-ttu-id="5ea27-148">组件参数由使用 `[Parameter]` 修饰的组件类上的专用属性定义。</span><span class="sxs-lookup"><span data-stu-id="5ea27-148">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="5ea27-149">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="5ea27-149">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="5ea27-150">更新组件的 `@functions` C# 代码：</span><span class="sxs-lookup"><span data-stu-id="5ea27-150">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="5ea27-151">添加使用 `[Parameter]` 属性修饰的 `IncrementAmount` 属性。</span><span class="sxs-lookup"><span data-stu-id="5ea27-151">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="5ea27-152">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="5ea27-152">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="5ea27-153">Components/Pages/Counter.razor（Blazor 中的 Pages/Counter.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="5ea27-153">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="5ea27-154">使用属性在 Home 组件的 `<Counter>` 元素中指定 `IncrementAmount` 参数。</span><span class="sxs-lookup"><span data-stu-id="5ea27-154">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="5ea27-155">将计数器递增值设置为 10。</span><span class="sxs-lookup"><span data-stu-id="5ea27-155">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="5ea27-156">Components/Pages/Index.razor（Blazor 中的 Pages/Index.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="5ea27-156">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Index.razor?highlight=7)]

1. <span data-ttu-id="5ea27-157">重新加载主页。</span><span class="sxs-lookup"><span data-stu-id="5ea27-157">Reload the Home page.</span></span> <span data-ttu-id="5ea27-158">每次选择“单击我”按钮时，计数器值递增 10。</span><span class="sxs-lookup"><span data-stu-id="5ea27-158">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="5ea27-159">“计数器”页上的计数器值递增 1。</span><span class="sxs-lookup"><span data-stu-id="5ea27-159">The counter on the Counter page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="5ea27-160">路由到组件</span><span class="sxs-lookup"><span data-stu-id="5ea27-160">Route to components</span></span>

<span data-ttu-id="5ea27-161">Counter.razor 文件顶部的 `@page` 指令指定此组件是路由终结点。</span><span class="sxs-lookup"><span data-stu-id="5ea27-161">The `@page` directive at the top of the *Counter.razor* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="5ea27-162">Counter 组件处理发送到 `/Counter` 的请求。</span><span class="sxs-lookup"><span data-stu-id="5ea27-162">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="5ea27-163">如果没有 `@page` 指令，组件将无法处理路由的请求，但仍可以被其他组件使用。</span><span class="sxs-lookup"><span data-stu-id="5ea27-163">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="5ea27-164">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="5ea27-164">Dependency injection</span></span>

<span data-ttu-id="5ea27-165">通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)，组件可以使用注册了应用服务容器的服务。</span><span class="sxs-lookup"><span data-stu-id="5ea27-165">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5ea27-166">使用 `@inject` 指令将服务注入到组件中。</span><span class="sxs-lookup"><span data-stu-id="5ea27-166">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="5ea27-167">检查示例应用中 FetchData 组件的指令。</span><span class="sxs-lookup"><span data-stu-id="5ea27-167">Examine the directives of the FetchData component in the sample app.</span></span>

<span data-ttu-id="5ea27-168">在 Razor 组件示例应用中，`WeatherForecastService` 服务注册为[单一实例](xref:fundamentals/dependency-injection#service-lifetimes)，因此整个应用中有一个服务实例。</span><span class="sxs-lookup"><span data-stu-id="5ea27-168">In the Razor Components sample app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="5ea27-169">`@inject` 指令用于将 `WeatherForecastService` 服务的实例注入到组件中。</span><span class="sxs-lookup"><span data-stu-id="5ea27-169">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="5ea27-170">*Components/Pages/FetchData.razor*：</span><span class="sxs-lookup"><span data-stu-id="5ea27-170">*Components/Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="5ea27-171">FetchData 组件使用注入的服务（作为 `ForecastService`）来检索 `WeatherForecast` 对象的数组：</span><span class="sxs-lookup"><span data-stu-id="5ea27-171">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="5ea27-172">在 Blazor 版示例应用中，注入了 `HttpClient`，以从 wwwroot/sample-data 文件夹的 weather.json 文件中获取天气预测数据：</span><span class="sxs-lookup"><span data-stu-id="5ea27-172">In the Blazor version of the sample app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="5ea27-173">*Pages/FetchData.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="5ea27-173">*Pages/FetchData.cshtml*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=7)]

<span data-ttu-id="5ea27-174">两个示例应用中都使用了 [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 循环来将每个预测实例呈现为“天气”数据表中的一行：</span><span class="sxs-lookup"><span data-stu-id="5ea27-174">In both sample apps, a [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="5ea27-175">生成待办项列表</span><span class="sxs-lookup"><span data-stu-id="5ea27-175">Build a todo list</span></span>

<span data-ttu-id="5ea27-176">向应用添加一个实现简单待办事项列表的新组件。</span><span class="sxs-lookup"><span data-stu-id="5ea27-176">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="5ea27-177">向示例应用添加实体文件：</span><span class="sxs-lookup"><span data-stu-id="5ea27-177">Add an empty file to the sample app:</span></span>

   * <span data-ttu-id="5ea27-178">对于 Razor 组件体验，向 Components/Pages 文件夹添加 Todo.razor 文件。</span><span class="sxs-lookup"><span data-stu-id="5ea27-178">For the Razor Components experience, add a *Todo.razor* file to the *Components/Pages* folder.</span></span>
   * <span data-ttu-id="5ea27-179">对于 Blazor 体验，向 Pages 文件夹添加 Todo.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="5ea27-179">For the Blazor experience, add a *Todo.cshtml* file to the *Pages* folder.</span></span>

1. <span data-ttu-id="5ea27-180">为组件提供初始标记：</span><span class="sxs-lookup"><span data-stu-id="5ea27-180">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="5ea27-181">将“待办事项”组件添加到导航栏。</span><span class="sxs-lookup"><span data-stu-id="5ea27-181">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="5ea27-182">应用布局中使用 NavMenu 组件（Blazor 中的 Components/Shared/NavMenu.razor 或 Shared/NavMenu.cshtml）。</span><span class="sxs-lookup"><span data-stu-id="5ea27-182">The NavMenu component (*Components/Shared/NavMenu.razor* or *Shared/NavMenu.cshtml* in Blazor) is used in the app's layout.</span></span> <span data-ttu-id="5ea27-183">布局是可以避免应用中出现重复内容的组件。</span><span class="sxs-lookup"><span data-stu-id="5ea27-183">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="5ea27-184">有关更多信息，请参见<xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="5ea27-184">For more information, see <xref:blazor/layouts>.</span></span>

   <span data-ttu-id="5ea27-185">通过在 Components/Shared/NavMenu.razor（Blazor 中为 Shared/NavMenu.cshtml）文件中的现有列表项下添加以下列表项标记，为“待办事项”组件添加一个 `<NavLink>`：</span><span class="sxs-lookup"><span data-stu-id="5ea27-185">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* in Blazor) file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="5ea27-186">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="5ea27-186">Rebuild and run the app.</span></span> <span data-ttu-id="5ea27-187">访问新的“待办事项”页面，确认指向“待办事项”组件的链接有效。</span><span class="sxs-lookup"><span data-stu-id="5ea27-187">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="5ea27-188">向项目的根目录添加“TodoItem.cs”文件，以保存一个用于表示待办项的类。</span><span class="sxs-lookup"><span data-stu-id="5ea27-188">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="5ea27-189">为 `TodoItem` 类使用以下 C# 代码：</span><span class="sxs-lookup"><span data-stu-id="5ea27-189">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/TodoItem.cs)]

1. <span data-ttu-id="5ea27-190">返回到 Todo 组件（Blazor 中的 Components/Pages/Todo.razor 或 Pages/Todo.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="5ea27-190">Return to the Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   * <span data-ttu-id="5ea27-191">在 `@functions` 块中为待办项添加字段。</span><span class="sxs-lookup"><span data-stu-id="5ea27-191">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="5ea27-192">Todo 组件使用此字段来维护待办项列表的状态。</span><span class="sxs-lookup"><span data-stu-id="5ea27-192">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="5ea27-193">添加无序列表标记和 `foreach` 循环，以将每个待办项呈现为列表项。</span><span class="sxs-lookup"><span data-stu-id="5ea27-193">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="5ea27-194">该应用需要 UI 元素来将待办项添加到列表。</span><span class="sxs-lookup"><span data-stu-id="5ea27-194">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="5ea27-195">在列表下方添加一个文本输入和一个按钮：</span><span class="sxs-lookup"><span data-stu-id="5ea27-195">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="5ea27-196">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="5ea27-196">Rebuild and run the app.</span></span> <span data-ttu-id="5ea27-197">选择“添加待办项”按钮时没有任何反应，因为没有事件处理程序连接到该按钮。</span><span class="sxs-lookup"><span data-stu-id="5ea27-197">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="5ea27-198">向 Todo 组件添加 `AddTodo` 方法，并使用 `onclick` 属性注册该方法以单击按钮：</span><span class="sxs-lookup"><span data-stu-id="5ea27-198">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="5ea27-199">选择按钮时，会调用 `AddTodo` C# 方法。</span><span class="sxs-lookup"><span data-stu-id="5ea27-199">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="5ea27-200">要获得新待办项标题，请添加 `newTodo` 字符串字段，并使用 `bind` 属性将其绑定到文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="5ea27-200">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. <span data-ttu-id="5ea27-201">更新 `AddTodo` 方法，将具有指定标题的 `TodoItem` 添加到列表。</span><span class="sxs-lookup"><span data-stu-id="5ea27-201">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="5ea27-202">通过将 `newTodo` 设置为空字符串来清除文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="5ea27-202">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="5ea27-203">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="5ea27-203">Rebuild and run the app.</span></span> <span data-ttu-id="5ea27-204">在待办项列表中添加一些待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="5ea27-204">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="5ea27-205">每个待办项的标题文本都可以编辑，复选框可以帮助用户跟踪已完成的项。</span><span class="sxs-lookup"><span data-stu-id="5ea27-205">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="5ea27-206">为每个待办项添加一个复选框输入，并将它的值绑定到 `IsDone` 属性。</span><span class="sxs-lookup"><span data-stu-id="5ea27-206">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="5ea27-207">将 `@todo.Title` 更改为绑定到 `@todo.Title` 的 `<input>` 元素：</span><span class="sxs-lookup"><span data-stu-id="5ea27-207">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="5ea27-208">若要验证这些值是否已绑定，请更新 `<h1>` 标头以显示尚未完成的待办项计数（`IsDone` 是 `false`）。</span><span class="sxs-lookup"><span data-stu-id="5ea27-208">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="5ea27-209">完成的 Todo 组件（Blazor 中的 Components/Pages/Todo.razor 或 Pages/Todo.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="5ea27-209">The completed Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Todo.razor)]

1. <span data-ttu-id="5ea27-210">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="5ea27-210">Rebuild and run the app.</span></span> <span data-ttu-id="5ea27-211">添加待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="5ea27-211">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="5ea27-212">发布和部署应用</span><span class="sxs-lookup"><span data-stu-id="5ea27-212">Publish and deploy the app</span></span>

<span data-ttu-id="5ea27-213">要发布应用，请参阅<xref:host-and-deploy/blazor/index>。</span><span class="sxs-lookup"><span data-stu-id="5ea27-213">To publish the app, see <xref:host-and-deploy/blazor/index>.</span></span>
