---
title: 生成第一个 Razor 组件应用
author: guardrex
description: 逐步生成 Razor 组件应用并了解 Razor 组件基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2019
uid: tutorials/first-razor-components-app
ms.openlocfilehash: 2a987b3f2e687cd9d4dffa2c573c938e68ea3cc8
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419360"
---
# <a name="build-your-first-razor-components-app"></a><span data-ttu-id="241a3-103">生成第一个 Razor 组件应用</span><span class="sxs-lookup"><span data-stu-id="241a3-103">Build your first Razor Components app</span></span>

<span data-ttu-id="241a3-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="241a3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="241a3-105">本教程演示如何使用 Razor 组件生成应用并演示 Razor 组件基本概念。</span><span class="sxs-lookup"><span data-stu-id="241a3-105">This tutorial shows you how to build an app with Razor Components and demonstrates basic Razor Components concepts.</span></span> <span data-ttu-id="241a3-106">可以使用基于 Razor 组件的项目（在 .NET Core 3.0 或更高版本中受支持）或使用基于 Blazor 的项目（在 .NET Core 未来版本中受支持）来利用本教程。</span><span class="sxs-lookup"><span data-stu-id="241a3-106">You can enjoy this tutorial using either a Razor Components-based project (supported in .NET Core 3.0 or later) or using a Blazor-based project (supported in a future release of .NET Core).</span></span>

<span data-ttu-id="241a3-107">若要体验使用 ASP.NET Core Razor 组件，可以执行以下操作（*建议*）：</span><span class="sxs-lookup"><span data-stu-id="241a3-107">For an experience using ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="241a3-108">按照 <xref:razor-components/get-started>中的指导创建基于 Razor 组件的项目。</span><span class="sxs-lookup"><span data-stu-id="241a3-108">Follow the guidance in <xref:razor-components/get-started> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="241a3-109">将项目命名为 `RazorComponents`。</span><span class="sxs-lookup"><span data-stu-id="241a3-109">Name the project `RazorComponents`.</span></span>

<span data-ttu-id="241a3-110">若要体验使用 Blazor，可以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="241a3-110">For an experience using Blazor:</span></span>

* <span data-ttu-id="241a3-111">按照 <xref:spa/blazor/get-started>中的指导创建基于 Blazor 的项目。</span><span class="sxs-lookup"><span data-stu-id="241a3-111">Follow the guidance in <xref:spa/blazor/get-started> to create a Blazor-based project.</span></span>
* <span data-ttu-id="241a3-112">将项目命名为 `Blazor`。</span><span class="sxs-lookup"><span data-stu-id="241a3-112">Name the project `Blazor`.</span></span>

<span data-ttu-id="241a3-113">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="241a3-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="241a3-114">有关先决条件，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="241a3-114">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="241a3-115">生成组件</span><span class="sxs-lookup"><span data-stu-id="241a3-115">Build components</span></span>

1. <span data-ttu-id="241a3-116">在 Components/Pages 文件夹中浏览应用的三个页面（Blazor 中的 Pages）：主页、计数器和提取数据。</span><span class="sxs-lookup"><span data-stu-id="241a3-116">Browse to each of the app's three pages in the *Components/Pages* folder (*Pages* in Blazor): Home, Counter, and Fetch data.</span></span> <span data-ttu-id="241a3-117">这些页面由 Razor 组件文件实现：Index.razor、Counter.razor 和FetchData.razor。</span><span class="sxs-lookup"><span data-stu-id="241a3-117">These pages are implemented by Razor Component files: *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span> <span data-ttu-id="241a3-118">（Blazor 将继续使用 .cshtml 文件扩展名：Index.cshtml、Counter.cshtml 和 FetchData.cshtml。）</span><span class="sxs-lookup"><span data-stu-id="241a3-118">(Blazor continues to use the *.cshtml* file extension: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.)</span></span>

1. <span data-ttu-id="241a3-119">在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="241a3-119">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="241a3-120">增加网页中的计数器值通常需要编写 JavaScript，但 Razor 组件使用 C# 提供了更好的方法。</span><span class="sxs-lookup"><span data-stu-id="241a3-120">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

1. <span data-ttu-id="241a3-121">检查 Counter.razor 文件中 Counter 组件的实现。</span><span class="sxs-lookup"><span data-stu-id="241a3-121">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="241a3-122">Components/Pages/Counter.razor（Blazor 中的 Pages/Counter.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="241a3-122">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="241a3-123">使用 HTML 定义 Counter 组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="241a3-123">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="241a3-124">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="241a3-124">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="241a3-125">HTML 标记和 C# 呈现逻辑在构建时转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="241a3-125">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="241a3-126">生成的 .NET 类的名称与文件名匹配。</span><span class="sxs-lookup"><span data-stu-id="241a3-126">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="241a3-127">组件类的成员在 `@functions` 块中定义。</span><span class="sxs-lookup"><span data-stu-id="241a3-127">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="241a3-128">在 `@functions` 块中，可以指定组件状态（属性、字段）和方法用于处理事件或定义其他组件逻辑。</span><span class="sxs-lookup"><span data-stu-id="241a3-128">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="241a3-129">然后，可以将这些成员用作组件呈现逻辑的一部分，并用于处理事件。</span><span class="sxs-lookup"><span data-stu-id="241a3-129">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="241a3-130">选中“单击我”按钮时：</span><span class="sxs-lookup"><span data-stu-id="241a3-130">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="241a3-131">调用 Counter 组件的已注册 `onclick` 处理程序（`IncrementCount` 方法）。</span><span class="sxs-lookup"><span data-stu-id="241a3-131">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="241a3-132">Counter 组件重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="241a3-132">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="241a3-133">将新的呈现树与前一个呈现树进行比较。</span><span class="sxs-lookup"><span data-stu-id="241a3-133">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="241a3-134">仅应用对文档对象模型 (DOM) 的修改。</span><span class="sxs-lookup"><span data-stu-id="241a3-134">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="241a3-135">显示的计数将会更新。</span><span class="sxs-lookup"><span data-stu-id="241a3-135">The displayed count is updated.</span></span>

1. <span data-ttu-id="241a3-136">修改 Counter 组件的 C# 逻辑，使计数递增 2 而不是 1。</span><span class="sxs-lookup"><span data-stu-id="241a3-136">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="241a3-137">重新生成并运行应用以查看更改。</span><span class="sxs-lookup"><span data-stu-id="241a3-137">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="241a3-138">选择“单击我”按钮，计数器的值将增加 2。</span><span class="sxs-lookup"><span data-stu-id="241a3-138">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="241a3-139">使用组件</span><span class="sxs-lookup"><span data-stu-id="241a3-139">Use components</span></span>

<span data-ttu-id="241a3-140">使用类似 HTML 的语法将组件加入到另一个组件中。</span><span class="sxs-lookup"><span data-stu-id="241a3-140">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="241a3-141">通过向 Index 组件添加 `<Counter />` 元素，将 Counter 组件添加到应用的 Index（主页）组件。</span><span class="sxs-lookup"><span data-stu-id="241a3-141">Add the Counter component to the app's Index (Home) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="241a3-142">如果在此体验中使用的是 Blazor，则 Survey Prompt 组件（`<SurveyPrompt>` 元素）位于 Index 组件中。</span><span class="sxs-lookup"><span data-stu-id="241a3-142">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="241a3-143">将 `<SurveyPrompt>` 元素替换为 `<Counter>` 元素。</span><span class="sxs-lookup"><span data-stu-id="241a3-143">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="241a3-144">Components/Pages/Index.razor（Blazor 中的 Pages/Index.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="241a3-144">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Index.razor?highlight=7)]

1. <span data-ttu-id="241a3-145">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="241a3-145">Rebuild and run the app.</span></span> <span data-ttu-id="241a3-146">主页有其自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="241a3-146">The Home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="241a3-147">组件参数</span><span class="sxs-lookup"><span data-stu-id="241a3-147">Component parameters</span></span>

<span data-ttu-id="241a3-148">组件也可以有参数。</span><span class="sxs-lookup"><span data-stu-id="241a3-148">Components can also have parameters.</span></span> <span data-ttu-id="241a3-149">组件参数由使用 `[Parameter]` 修饰的组件类上的专用属性定义。</span><span class="sxs-lookup"><span data-stu-id="241a3-149">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="241a3-150">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="241a3-150">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="241a3-151">更新组件的 `@functions` C# 代码：</span><span class="sxs-lookup"><span data-stu-id="241a3-151">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="241a3-152">添加使用 `[Parameter]` 属性修饰的 `IncrementAmount` 属性。</span><span class="sxs-lookup"><span data-stu-id="241a3-152">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="241a3-153">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="241a3-153">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="241a3-154">Components/Pages/Counter.razor（Blazor 中的 Pages/Counter.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="241a3-154">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="241a3-155">使用属性在 Home 组件的 `<Counter>` 元素中指定 `IncrementAmount` 参数。</span><span class="sxs-lookup"><span data-stu-id="241a3-155">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="241a3-156">将计数器递增值设置为 10。</span><span class="sxs-lookup"><span data-stu-id="241a3-156">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="241a3-157">Components/Pages/Index.razor（Blazor 中的 Pages/Index.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="241a3-157">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Index.razor?highlight=7)]

1. <span data-ttu-id="241a3-158">重新加载主页。</span><span class="sxs-lookup"><span data-stu-id="241a3-158">Reload the Home page.</span></span> <span data-ttu-id="241a3-159">每次选择“单击我”按钮时，计数器值递增 10。</span><span class="sxs-lookup"><span data-stu-id="241a3-159">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="241a3-160">“计数器”页上的计数器值递增 1。</span><span class="sxs-lookup"><span data-stu-id="241a3-160">The counter on the Counter page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="241a3-161">路由到组件</span><span class="sxs-lookup"><span data-stu-id="241a3-161">Route to components</span></span>

<span data-ttu-id="241a3-162">Counter.razor 文件顶部的 `@page` 指令指定此组件是路由终结点。</span><span class="sxs-lookup"><span data-stu-id="241a3-162">The `@page` directive at the top of the *Counter.razor* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="241a3-163">Counter 组件处理发送到 `/Counter` 的请求。</span><span class="sxs-lookup"><span data-stu-id="241a3-163">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="241a3-164">如果没有 `@page` 指令，组件将无法处理路由的请求，但仍可以被其他组件使用。</span><span class="sxs-lookup"><span data-stu-id="241a3-164">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="241a3-165">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="241a3-165">Dependency injection</span></span>

<span data-ttu-id="241a3-166">通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)，组件可以使用注册了应用服务容器的服务。</span><span class="sxs-lookup"><span data-stu-id="241a3-166">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="241a3-167">使用 `@inject` 指令将服务注入到组件中。</span><span class="sxs-lookup"><span data-stu-id="241a3-167">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="241a3-168">检查示例应用中 FetchData 组件的指令。</span><span class="sxs-lookup"><span data-stu-id="241a3-168">Examine the directives of the FetchData component in the sample app.</span></span>

<span data-ttu-id="241a3-169">在 Razor 组件示例应用中，`WeatherForecastService` 服务注册为[单一实例](xref:fundamentals/dependency-injection#service-lifetimes)，因此整个应用中有一个服务实例。</span><span class="sxs-lookup"><span data-stu-id="241a3-169">In the Razor Components sample app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="241a3-170">`@inject` 指令用于将 `WeatherForecastService` 服务的实例注入到组件中。</span><span class="sxs-lookup"><span data-stu-id="241a3-170">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="241a3-171">*Components/Pages/FetchData.razor*：</span><span class="sxs-lookup"><span data-stu-id="241a3-171">*Components/Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="241a3-172">FetchData 组件使用注入的服务（作为 `ForecastService`）来检索 `WeatherForecast` 对象的数组：</span><span class="sxs-lookup"><span data-stu-id="241a3-172">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="241a3-173">在 Blazor 版示例应用中，注入了 `HttpClient`，以从 wwwroot/sample-data 文件夹的 weather.json 文件中获取天气预测数据：</span><span class="sxs-lookup"><span data-stu-id="241a3-173">In the Blazor version of the sample app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="241a3-174">*Pages/FetchData.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="241a3-174">*Pages/FetchData.cshtml*:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=7)]

<span data-ttu-id="241a3-175">两个示例应用中都使用了 [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 循环来将每个预测实例呈现为“天气”数据表中的一行：</span><span class="sxs-lookup"><span data-stu-id="241a3-175">In both sample apps, a [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="241a3-176">生成待办项列表</span><span class="sxs-lookup"><span data-stu-id="241a3-176">Build a todo list</span></span>

<span data-ttu-id="241a3-177">向应用添加一个实现简单待办事项列表的新组件。</span><span class="sxs-lookup"><span data-stu-id="241a3-177">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="241a3-178">向示例应用添加实体文件：</span><span class="sxs-lookup"><span data-stu-id="241a3-178">Add an empty file to the sample app:</span></span>

   * <span data-ttu-id="241a3-179">对于 Razor 组件体验，向 Components/Pages 文件夹添加 Todo.razor 文件。</span><span class="sxs-lookup"><span data-stu-id="241a3-179">For the Razor Components experience, add a *Todo.razor* file to the *Components/Pages* folder.</span></span>
   * <span data-ttu-id="241a3-180">对于 Blazor 体验，向 Pages 文件夹添加 Todo.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="241a3-180">For the Blazor experience, add a *Todo.cshtml* file to the *Pages* folder.</span></span>

1. <span data-ttu-id="241a3-181">为组件提供初始标记：</span><span class="sxs-lookup"><span data-stu-id="241a3-181">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="241a3-182">将“待办事项”组件添加到导航栏。</span><span class="sxs-lookup"><span data-stu-id="241a3-182">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="241a3-183">应用布局中使用 NavMenu 组件（Blazor 中的 Components/Shared/NavMenu.razor 或 Shared/NavMenu.cshtml）。</span><span class="sxs-lookup"><span data-stu-id="241a3-183">The NavMenu component (*Components/Shared/NavMenu.razor* or *Shared/NavMenu.cshtml* in Blazor) is used in the app's layout.</span></span> <span data-ttu-id="241a3-184">布局是可以避免应用中出现重复内容的组件。</span><span class="sxs-lookup"><span data-stu-id="241a3-184">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="241a3-185">有关更多信息，请参见<xref:razor-components/layouts>。</span><span class="sxs-lookup"><span data-stu-id="241a3-185">For more information, see <xref:razor-components/layouts>.</span></span>

   <span data-ttu-id="241a3-186">通过在 Components/Shared/NavMenu.razor（Blazor 中为 Shared/NavMenu.cshtml）文件中的现有列表项下添加以下列表项标记，为“待办事项”组件添加一个 `<NavLink>`：</span><span class="sxs-lookup"><span data-stu-id="241a3-186">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* in Blazor) file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="241a3-187">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="241a3-187">Rebuild and run the app.</span></span> <span data-ttu-id="241a3-188">访问新的“待办事项”页面，确认指向“待办事项”组件的链接有效。</span><span class="sxs-lookup"><span data-stu-id="241a3-188">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="241a3-189">向项目的根目录添加“TodoItem.cs”文件，以保存一个用于表示待办项的类。</span><span class="sxs-lookup"><span data-stu-id="241a3-189">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="241a3-190">为 `TodoItem` 类使用以下 C# 代码：</span><span class="sxs-lookup"><span data-stu-id="241a3-190">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/TodoItem.cs)]

1. <span data-ttu-id="241a3-191">返回到 Todo 组件（Blazor 中的 Components/Pages/Todo.razor 或 Pages/Todo.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="241a3-191">Return to the Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   * <span data-ttu-id="241a3-192">在 `@functions` 块中为待办项添加字段。</span><span class="sxs-lookup"><span data-stu-id="241a3-192">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="241a3-193">Todo 组件使用此字段来维护待办项列表的状态。</span><span class="sxs-lookup"><span data-stu-id="241a3-193">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="241a3-194">添加无序列表标记和 `foreach` 循环，以将每个待办项呈现为列表项。</span><span class="sxs-lookup"><span data-stu-id="241a3-194">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="241a3-195">该应用需要 UI 元素来将待办项添加到列表。</span><span class="sxs-lookup"><span data-stu-id="241a3-195">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="241a3-196">在列表下方添加一个文本输入和一个按钮：</span><span class="sxs-lookup"><span data-stu-id="241a3-196">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="241a3-197">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="241a3-197">Rebuild and run the app.</span></span> <span data-ttu-id="241a3-198">选择“添加待办项”按钮时没有任何反应，因为没有事件处理程序连接到该按钮。</span><span class="sxs-lookup"><span data-stu-id="241a3-198">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="241a3-199">向 Todo 组件添加 `AddTodo` 方法，并使用 `onclick` 属性注册该方法以单击按钮：</span><span class="sxs-lookup"><span data-stu-id="241a3-199">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="241a3-200">选择按钮时，会调用 `AddTodo` C# 方法。</span><span class="sxs-lookup"><span data-stu-id="241a3-200">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="241a3-201">要获得新待办项标题，请添加 `newTodo` 字符串字段，并使用 `bind` 属性将其绑定到文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="241a3-201">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo" />
   ```

1. <span data-ttu-id="241a3-202">更新 `AddTodo` 方法，将具有指定标题的 `TodoItem` 添加到列表。</span><span class="sxs-lookup"><span data-stu-id="241a3-202">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="241a3-203">通过将 `newTodo` 设置为空字符串来清除文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="241a3-203">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="241a3-204">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="241a3-204">Rebuild and run the app.</span></span> <span data-ttu-id="241a3-205">在待办项列表中添加一些待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="241a3-205">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="241a3-206">每个待办项的标题文本都可以编辑，复选框可以帮助用户跟踪已完成的项。</span><span class="sxs-lookup"><span data-stu-id="241a3-206">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="241a3-207">为每个待办项添加一个复选框输入，并将它的值绑定到 `IsDone` 属性。</span><span class="sxs-lookup"><span data-stu-id="241a3-207">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="241a3-208">将 `@todo.Title` 更改为绑定到 `@todo.Title` 的 `<input>` 元素：</span><span class="sxs-lookup"><span data-stu-id="241a3-208">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="241a3-209">若要验证这些值是否已绑定，请更新 `<h1>` 标头以显示尚未完成的待办项计数（`IsDone` 是 `false`）。</span><span class="sxs-lookup"><span data-stu-id="241a3-209">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="241a3-210">完成的 Todo 组件（Blazor 中的 Components/Pages/Todo.razor 或 Pages/Todo.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="241a3-210">The completed Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Todo.razor)]

1. <span data-ttu-id="241a3-211">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="241a3-211">Rebuild and run the app.</span></span> <span data-ttu-id="241a3-212">添加待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="241a3-212">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="241a3-213">发布和部署应用</span><span class="sxs-lookup"><span data-stu-id="241a3-213">Publish and deploy the app</span></span>

<span data-ttu-id="241a3-214">要发布应用，请参阅<xref:host-and-deploy/razor-components/index#publish-the-app>。</span><span class="sxs-lookup"><span data-stu-id="241a3-214">To publish the app, see <xref:host-and-deploy/razor-components/index#publish-the-app>.</span></span>
