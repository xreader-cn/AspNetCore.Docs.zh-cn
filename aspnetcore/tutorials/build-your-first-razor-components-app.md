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
# <a name="build-your-first-razor-components-app"></a><span data-ttu-id="e63f5-103">生成第一个 Razor 组件应用</span><span class="sxs-lookup"><span data-stu-id="e63f5-103">Build your first Razor Components app</span></span>

<span data-ttu-id="e63f5-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e63f5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="e63f5-105">本教程演示如何使用 Razor 组件生成应用并演示 Razor 组件基本概念。</span><span class="sxs-lookup"><span data-stu-id="e63f5-105">This tutorial shows you how to build an app with Razor Components and demonstrates basic Razor Components concepts.</span></span> <span data-ttu-id="e63f5-106">可以使用基于 Razor 组件的项目（在 .NET Core 3.0 或更高版本中受支持）或使用基于 Blazor 的项目（在 .NET Core 未来版本中受支持）来利用本教程。</span><span class="sxs-lookup"><span data-stu-id="e63f5-106">You can enjoy this tutorial using either a Razor Components-based project (supported in .NET Core 3.0 or later) or using a Blazor-based project (supported in a future release of .NET Core).</span></span>

<span data-ttu-id="e63f5-107">若要体验使用 ASP.NET Core Razor 组件，可以执行以下操作（*建议*）：</span><span class="sxs-lookup"><span data-stu-id="e63f5-107">For an experience using ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="e63f5-108">按照 <xref:razor-components/get-started>中的指导创建基于 Razor 组件的项目。</span><span class="sxs-lookup"><span data-stu-id="e63f5-108">Follow the guidance in <xref:razor-components/get-started> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="e63f5-109">将项目命名为 `RazorComponents`。</span><span class="sxs-lookup"><span data-stu-id="e63f5-109">Name the project `RazorComponents`.</span></span>
* <span data-ttu-id="e63f5-110">从“Razor 组件”模板创建多项目解决方案。</span><span class="sxs-lookup"><span data-stu-id="e63f5-110">A multi-project solution is created from the Razor Components template.</span></span> <span data-ttu-id="e63f5-111">将 Razor 组件项目生成为 RazorComponents.App。</span><span class="sxs-lookup"><span data-stu-id="e63f5-111">The Razor Components project is generated as *RazorComponents.App*.</span></span>

<span data-ttu-id="e63f5-112">若要体验使用 Blazor，可以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e63f5-112">For an experience using Blazor:</span></span>

* <span data-ttu-id="e63f5-113">按照 <xref:spa/blazor/get-started>中的指导创建基于 Blazor 的项目。</span><span class="sxs-lookup"><span data-stu-id="e63f5-113">Follow the guidance in <xref:spa/blazor/get-started> to create a Blazor-based project.</span></span>
* <span data-ttu-id="e63f5-114">将项目命名为 `Blazor`。</span><span class="sxs-lookup"><span data-stu-id="e63f5-114">Name the project `Blazor`.</span></span>
* <span data-ttu-id="e63f5-115">从 Blazor 模板创建单项目解决方案。</span><span class="sxs-lookup"><span data-stu-id="e63f5-115">A single-project solution is created from the Blazor template.</span></span>

<span data-ttu-id="e63f5-116">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="e63f5-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="e63f5-117">有关先决条件，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="e63f5-117">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="e63f5-118">生成组件</span><span class="sxs-lookup"><span data-stu-id="e63f5-118">Build components</span></span>

1. <span data-ttu-id="e63f5-119">浏览到每个应用的三个页面：主页、计数器和提取数据。</span><span class="sxs-lookup"><span data-stu-id="e63f5-119">Browse to each of the app's three pages: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="e63f5-120">这些页面由“Pages”文件夹中的 Razor 文件实现：Index.cshtml、Counter.cshtml 和 FetchData.cshtml。</span><span class="sxs-lookup"><span data-stu-id="e63f5-120">These pages are implemented by Razor files in the *Pages* folder: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.</span></span>

1. <span data-ttu-id="e63f5-121">在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="e63f5-121">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="e63f5-122">增加网页中的计数器值通常需要编写 JavaScript，但 Razor 组件使用 C# 提供了更好的方法。</span><span class="sxs-lookup"><span data-stu-id="e63f5-122">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

1. <span data-ttu-id="e63f5-123">检查“Counter.cshtml”文件中 Counter 组件的实现。</span><span class="sxs-lookup"><span data-stu-id="e63f5-123">Examine the implementation of the Counter component in the *Counter.cshtml* file.</span></span>

   <span data-ttu-id="e63f5-124">*Pages/Counter.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e63f5-124">*Pages/Counter.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter1.cshtml)]

   <span data-ttu-id="e63f5-125">使用 HTML 定义 Counter 组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="e63f5-125">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="e63f5-126">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="e63f5-126">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="e63f5-127">HTML 标记和 C# 呈现逻辑在构建时转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="e63f5-127">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="e63f5-128">生成的 .NET 类的名称与文件名匹配。</span><span class="sxs-lookup"><span data-stu-id="e63f5-128">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="e63f5-129">组件类的成员在 `@functions` 块中定义。</span><span class="sxs-lookup"><span data-stu-id="e63f5-129">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="e63f5-130">在 `@functions` 块中，可以指定组件状态（属性、字段）和方法用于处理事件或定义其他组件逻辑。</span><span class="sxs-lookup"><span data-stu-id="e63f5-130">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="e63f5-131">然后，可以将这些成员用作组件呈现逻辑的一部分，并用于处理事件。</span><span class="sxs-lookup"><span data-stu-id="e63f5-131">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="e63f5-132">选中“单击我”按钮时：</span><span class="sxs-lookup"><span data-stu-id="e63f5-132">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="e63f5-133">调用 Counter 组件的已注册 `onclick` 处理程序（`IncrementCount` 方法）。</span><span class="sxs-lookup"><span data-stu-id="e63f5-133">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="e63f5-134">Counter 组件重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="e63f5-134">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="e63f5-135">将新的呈现树与前一个呈现树进行比较。</span><span class="sxs-lookup"><span data-stu-id="e63f5-135">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="e63f5-136">仅应用对文档对象模型 (DOM) 的修改。</span><span class="sxs-lookup"><span data-stu-id="e63f5-136">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="e63f5-137">显示的计数将会更新。</span><span class="sxs-lookup"><span data-stu-id="e63f5-137">The displayed count is updated.</span></span>

1. <span data-ttu-id="e63f5-138">修改 Counter 组件的 C# 逻辑，使计数递增 2 而不是 1。</span><span class="sxs-lookup"><span data-stu-id="e63f5-138">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter2.cshtml?highlight=14)]

1. <span data-ttu-id="e63f5-139">重新生成并运行应用以查看更改。</span><span class="sxs-lookup"><span data-stu-id="e63f5-139">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="e63f5-140">选择“单击我”按钮，计数器的值将增加 2。</span><span class="sxs-lookup"><span data-stu-id="e63f5-140">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="e63f5-141">使用组件</span><span class="sxs-lookup"><span data-stu-id="e63f5-141">Use components</span></span>

<span data-ttu-id="e63f5-142">使用类似 HTML 的语法将组件加入到另一个组件中。</span><span class="sxs-lookup"><span data-stu-id="e63f5-142">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="e63f5-143">通过向 Index 组件添加 `<Counter />` 元素，将 Counter 组件添加到应用的 Index（主页）组件。</span><span class="sxs-lookup"><span data-stu-id="e63f5-143">Add the Counter component to the app's Index (home page) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="e63f5-144">如果在此体验中使用的是 Blazor，则 Survey Prompt 组件（`<SurveyPrompt>` 元素）位于 Index 组件中。</span><span class="sxs-lookup"><span data-stu-id="e63f5-144">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="e63f5-145">将 `<SurveyPrompt>` 元素替换为 `<Counter>` 元素。</span><span class="sxs-lookup"><span data-stu-id="e63f5-145">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="e63f5-146">Pages/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e63f5-146">*Pages/Index.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Index.cshtml?highlight=7)]

1. <span data-ttu-id="e63f5-147">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="e63f5-147">Rebuild and run the app.</span></span> <span data-ttu-id="e63f5-148">主页本身具有自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="e63f5-148">The home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="e63f5-149">组件参数</span><span class="sxs-lookup"><span data-stu-id="e63f5-149">Component parameters</span></span>

<span data-ttu-id="e63f5-150">组件也可以有参数。</span><span class="sxs-lookup"><span data-stu-id="e63f5-150">Components can also have parameters.</span></span> <span data-ttu-id="e63f5-151">组件参数由使用 `[Parameter]` 修饰的组件类上的专用属性定义。</span><span class="sxs-lookup"><span data-stu-id="e63f5-151">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="e63f5-152">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="e63f5-152">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="e63f5-153">更新组件的 `@functions` C# 代码：</span><span class="sxs-lookup"><span data-stu-id="e63f5-153">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="e63f5-154">添加使用 `[Parameter]` 属性修饰的 `IncrementAmount` 属性。</span><span class="sxs-lookup"><span data-stu-id="e63f5-154">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="e63f5-155">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="e63f5-155">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="e63f5-156">*Pages/Counter.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="e63f5-156">*Pages/Counter.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Counter.cshtml?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="e63f5-157">使用属性在 Home 组件的 `<Counter>` 元素中指定 `IncrementAmount` 参数。</span><span class="sxs-lookup"><span data-stu-id="e63f5-157">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="e63f5-158">将计数器递增值设置为 10。</span><span class="sxs-lookup"><span data-stu-id="e63f5-158">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="e63f5-159">Pages/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="e63f5-159">*Pages/Index.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Index.cshtml?highlight=7)]

1. <span data-ttu-id="e63f5-160">重载页面。</span><span class="sxs-lookup"><span data-stu-id="e63f5-160">Reload the page.</span></span> <span data-ttu-id="e63f5-161">每次选择“单击我”按钮时，主页计数器值将增加 10。</span><span class="sxs-lookup"><span data-stu-id="e63f5-161">The home page counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="e63f5-162">“计数器”页上的计数器值将递增 1。</span><span class="sxs-lookup"><span data-stu-id="e63f5-162">The counter on the *Counter* page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="e63f5-163">路由到组件</span><span class="sxs-lookup"><span data-stu-id="e63f5-163">Route to components</span></span>

<span data-ttu-id="e63f5-164">“Counter.cshtml”文件顶部的 `@page` 指令指定此组件是路由终结点。</span><span class="sxs-lookup"><span data-stu-id="e63f5-164">The `@page` directive at the top of the *Counter.cshtml* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="e63f5-165">Counter 组件处理发送到 `/Counter` 的请求。</span><span class="sxs-lookup"><span data-stu-id="e63f5-165">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="e63f5-166">如果没有 `@page` 指令，组件将无法处理路由的请求，但仍可以被其他组件使用。</span><span class="sxs-lookup"><span data-stu-id="e63f5-166">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="e63f5-167">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="e63f5-167">Dependency injection</span></span>

<span data-ttu-id="e63f5-168">通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)，组件可以使用注册了应用服务容器的服务。</span><span class="sxs-lookup"><span data-stu-id="e63f5-168">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e63f5-169">使用 `@inject` 指令将服务注入到组件中。</span><span class="sxs-lookup"><span data-stu-id="e63f5-169">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="e63f5-170">检查 FetchData 组件的指令 (Pages/FetchData.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="e63f5-170">Examine the directives of the FetchData component (*Pages/FetchData.cshtml*).</span></span> <span data-ttu-id="e63f5-171">`@inject` 指令用于将 `WeatherForecastService` 服务的实例注入到组件中：</span><span class="sxs-lookup"><span data-stu-id="e63f5-171">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=3)]

<span data-ttu-id="e63f5-172">`WeatherForecastService` 服务注册为[单一实例](xref:fundamentals/dependency-injection#service-lifetimes)，因此整个应用中有一个服务实例。</span><span class="sxs-lookup"><span data-stu-id="e63f5-172">The `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span>

<span data-ttu-id="e63f5-173">FetchData 组件使用注入的服务（作为 `ForecastService`）来检索 `WeatherForecast` 对象的数组：</span><span class="sxs-lookup"><span data-stu-id="e63f5-173">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData2.cshtml?highlight=6)]

<span data-ttu-id="e63f5-174">[@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 循环用于将每个预测实例呈现为“天气”数据表中的一行：</span><span class="sxs-lookup"><span data-stu-id="e63f5-174">A [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData3.cshtml?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="e63f5-175">生成待办项列表</span><span class="sxs-lookup"><span data-stu-id="e63f5-175">Build a todo list</span></span>

<span data-ttu-id="e63f5-176">向应用程序添加一个实现简单待办项列表的新页面。</span><span class="sxs-lookup"><span data-stu-id="e63f5-176">Add a new page to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="e63f5-177">向“Pages”文件夹添加一个名为 Todo.cshtml 的空文件。</span><span class="sxs-lookup"><span data-stu-id="e63f5-177">Add an empty file to the *Pages* folder named *Todo.cshtml*.</span></span>

1. <span data-ttu-id="e63f5-178">为页面提供初始标记：</span><span class="sxs-lookup"><span data-stu-id="e63f5-178">Provide the initial markup for the page:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="e63f5-179">将“待办项”页面添加到导航栏。</span><span class="sxs-lookup"><span data-stu-id="e63f5-179">Add the Todo page to the navigation bar.</span></span>

   <span data-ttu-id="e63f5-180">NavMenu 组件 (Shared/NavMenu.csthml) 用于应用的布局。</span><span class="sxs-lookup"><span data-stu-id="e63f5-180">The NavMenu component (*Shared/NavMenu.csthml*) is used in the app's layout.</span></span> <span data-ttu-id="e63f5-181">布局是可以避免应用中出现重复内容的组件。</span><span class="sxs-lookup"><span data-stu-id="e63f5-181">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="e63f5-182">有关更多信息，请参见<xref:razor-components/layouts>。</span><span class="sxs-lookup"><span data-stu-id="e63f5-182">For more information, see <xref:razor-components/layouts>.</span></span>

   <span data-ttu-id="e63f5-183">通过在“Shared/NavMenu.csthml”文件中的现有列表项下添加以下列表项标记，为“待办项”页面添加一个 `<NavLink>`：</span><span class="sxs-lookup"><span data-stu-id="e63f5-183">Add a `<NavLink>` for the Todo page by adding the following list item markup below the existing list items in the *Shared/NavMenu.csthml* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="e63f5-184">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="e63f5-184">Rebuild and run the app.</span></span> <span data-ttu-id="e63f5-185">访问新的“待办项”页面，确认指向“待办项”页面的链接有效。</span><span class="sxs-lookup"><span data-stu-id="e63f5-185">Visit the new Todo page to confirm that the link to the Todo page works.</span></span>

1. <span data-ttu-id="e63f5-186">向项目的根目录添加“TodoItem.cs”文件，以保存一个用于表示待办项的类。</span><span class="sxs-lookup"><span data-stu-id="e63f5-186">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="e63f5-187">为 `TodoItem` 类使用以下 C# 代码：</span><span class="sxs-lookup"><span data-stu-id="e63f5-187">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/TodoItem.cs)]

1. <span data-ttu-id="e63f5-188">返回到 Todo 组件 (Todo.cshtml)：</span><span class="sxs-lookup"><span data-stu-id="e63f5-188">Return to the Todo component (*Todo.cshtml*):</span></span>

   * <span data-ttu-id="e63f5-189">在 `@functions` 块中为待办项添加字段。</span><span class="sxs-lookup"><span data-stu-id="e63f5-189">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="e63f5-190">Todo 组件使用此字段来维护待办项列表的状态。</span><span class="sxs-lookup"><span data-stu-id="e63f5-190">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="e63f5-191">添加无序列表标记和 `foreach` 循环，以将每个待办项呈现为列表项。</span><span class="sxs-lookup"><span data-stu-id="e63f5-191">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData4.cshtml?highlight=5-10,12-14)]

1. <span data-ttu-id="e63f5-192">该应用需要 UI 元素来将待办项添加到列表。</span><span class="sxs-lookup"><span data-stu-id="e63f5-192">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="e63f5-193">在列表下方添加一个文本输入和一个按钮：</span><span class="sxs-lookup"><span data-stu-id="e63f5-193">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData5.cshtml?highlight=12-13)]

1. <span data-ttu-id="e63f5-194">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="e63f5-194">Rebuild and run the app.</span></span> <span data-ttu-id="e63f5-195">选择“添加待办项”按钮时没有任何反应，因为没有事件处理程序连接到该按钮。</span><span class="sxs-lookup"><span data-stu-id="e63f5-195">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="e63f5-196">向 Todo 组件添加 `AddTodo` 方法，并使用 `onclick` 属性注册该方法以单击按钮：</span><span class="sxs-lookup"><span data-stu-id="e63f5-196">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData6.cshtml?highlight=2,7-10)]

   <span data-ttu-id="e63f5-197">选择按钮时，会调用 `AddTodo` C# 方法。</span><span class="sxs-lookup"><span data-stu-id="e63f5-197">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="e63f5-198">要获得新待办项标题，请添加 `newTodo` 字符串字段，并使用 `bind` 属性将其绑定到文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="e63f5-198">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData7.cshtml?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo" />
   ```

1. <span data-ttu-id="e63f5-199">更新 `AddTodo` 方法，将具有指定标题的 `TodoItem` 添加到列表。</span><span class="sxs-lookup"><span data-stu-id="e63f5-199">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="e63f5-200">通过将 `newTodo` 设置为空字符串来清除文本输入的值：</span><span class="sxs-lookup"><span data-stu-id="e63f5-200">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData8.cshtml?highlight=19-26)]

1. <span data-ttu-id="e63f5-201">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="e63f5-201">Rebuild and run the app.</span></span> <span data-ttu-id="e63f5-202">在待办项列表中添加一些待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="e63f5-202">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="e63f5-203">每个待办项的标题文本都可以编辑，复选框可以帮助用户跟踪已完成的项。</span><span class="sxs-lookup"><span data-stu-id="e63f5-203">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="e63f5-204">为每个待办项添加一个复选框输入，并将它的值绑定到 `IsDone` 属性。</span><span class="sxs-lookup"><span data-stu-id="e63f5-204">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="e63f5-205">将 `@todo.Title` 更改为绑定到 `@todo.Title` 的 `<input>` 元素：</span><span class="sxs-lookup"><span data-stu-id="e63f5-205">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData9.cshtml?highlight=5-6)]

1. <span data-ttu-id="e63f5-206">若要验证这些值是否已绑定，请更新 `<h1>` 标头以显示尚未完成的待办项计数（`IsDone` 是 `false`）。</span><span class="sxs-lookup"><span data-stu-id="e63f5-206">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="e63f5-207">完成的 Todo 组件 (Todo.cshtml)：</span><span class="sxs-lookup"><span data-stu-id="e63f5-207">The completed Todo component (*Todo.cshtml*):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Todo.cshtml)]

1. <span data-ttu-id="e63f5-208">重新生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="e63f5-208">Rebuild and run the app.</span></span> <span data-ttu-id="e63f5-209">添加待办项以测试新代码。</span><span class="sxs-lookup"><span data-stu-id="e63f5-209">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="e63f5-210">发布和部署应用</span><span class="sxs-lookup"><span data-stu-id="e63f5-210">Publish and deploy the app</span></span>

<span data-ttu-id="e63f5-211">要发布应用，请参阅<xref:host-and-deploy/razor-components/index#publish-the-app>。</span><span class="sxs-lookup"><span data-stu-id="e63f5-211">To publish the app, see <xref:host-and-deploy/razor-components/index#publish-the-app>.</span></span>
