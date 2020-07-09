---
title: 创建和使用 ASP.NET Core Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件，包括如何绑定到数据、处理事件和管理组件生命周期。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/index
ms.openlocfilehash: 23aab2504368559b8d3dd21b3c0896ffc3348e2f
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059813"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="f91b1-103">创建和使用 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="f91b1-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="f91b1-104">作者：[Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27) 和 [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="f91b1-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="f91b1-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f91b1-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="f91b1-106"> 应用是使用组件构建的。</span><span class="sxs-lookup"><span data-stu-id="f91b1-106"> apps are built using *components*.</span></span> <span data-ttu-id="f91b1-107">组件是自包含的用户界面 (UI) 块，例如页、对话框或窗体。</span><span class="sxs-lookup"><span data-stu-id="f91b1-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="f91b1-108">组件包含插入数据或响应 UI 事件所需的 HTML 标记和处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="f91b1-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="f91b1-109">组件非常灵活且轻量。</span><span class="sxs-lookup"><span data-stu-id="f91b1-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="f91b1-110">可在项目之间嵌套、重复使用和共享。</span><span class="sxs-lookup"><span data-stu-id="f91b1-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="f91b1-111">组件类</span><span class="sxs-lookup"><span data-stu-id="f91b1-111">Component classes</span></span>

<span data-ttu-id="f91b1-112">组件是使用 C# 和 HTML 标记的组合在 [Razor](xref:mvc/views/razor) 组件文件 (`.razor`) 中实现的。</span><span class="sxs-lookup"><span data-stu-id="f91b1-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="f91b1-113">Blazor 中的组件正式称为 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a>Razor<span data-ttu-id="f91b1-114"> 语法</span><span class="sxs-lookup"><span data-stu-id="f91b1-114"> syntax</span></span>

Blazor<span data-ttu-id="f91b1-115"> 应用中的 Razor 组件广泛使用 Razor 语法。</span><span class="sxs-lookup"><span data-stu-id="f91b1-115"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="f91b1-116">如果你不熟悉 Razor 标记语言，建议先阅读 <xref:mvc/views/razor>，然后再继续。</span><span class="sxs-lookup"><span data-stu-id="f91b1-116">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="f91b1-117">访问 Razor 语法上的内容时，请特别注意以下各节：</span><span class="sxs-lookup"><span data-stu-id="f91b1-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="f91b1-118">[指令](xref:mvc/views/razor#directives)：前缀为 `@` 的保留关键字，通常会更改组件标记的分析或运行方式。</span><span class="sxs-lookup"><span data-stu-id="f91b1-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="f91b1-119">[指令特性](xref:mvc/views/razor#directive-attributes)：前缀为 `@` 的保留关键字，通常会更改组件元素的分析或运行方式。</span><span class="sxs-lookup"><span data-stu-id="f91b1-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="f91b1-120">名称</span><span class="sxs-lookup"><span data-stu-id="f91b1-120">Names</span></span>

<span data-ttu-id="f91b1-121">组件的名称必须以大写字符开头。</span><span class="sxs-lookup"><span data-stu-id="f91b1-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="f91b1-122">例如，`MyCoolComponent.razor` 有效，而 `myCoolComponent.razor` 无效。</span><span class="sxs-lookup"><span data-stu-id="f91b1-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="f91b1-123">路由</span><span class="sxs-lookup"><span data-stu-id="f91b1-123">Routing</span></span>

<span data-ttu-id="f91b1-124">可以通过为应用中的每个可访问组件提供路由模板来实现 Blazor 中的路由。</span><span class="sxs-lookup"><span data-stu-id="f91b1-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="f91b1-125">编译具有 [`@page`][9] 指令的 Razor 文件时，将为生成的类提供指定路由模板的 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>。</span><span class="sxs-lookup"><span data-stu-id="f91b1-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="f91b1-126">在运行时，路由器将使用 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 查找组件类，并呈现具有与请求的 URL 匹配的路由模板的任何组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="f91b1-127">有关详细信息，请参阅 <xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="f91b1-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="f91b1-128">标记</span><span class="sxs-lookup"><span data-stu-id="f91b1-128">Markup</span></span>

<span data-ttu-id="f91b1-129">使用 HTML 定义组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="f91b1-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="f91b1-130">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 *Razor* 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="f91b1-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="f91b1-131">在编译应用时，HTML 标记和 C# 呈现逻辑转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="f91b1-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="f91b1-132">生成的类的名称与文件名匹配。</span><span class="sxs-lookup"><span data-stu-id="f91b1-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="f91b1-133">组件类的成员在 [`@code`][1] 块中定义。</span><span class="sxs-lookup"><span data-stu-id="f91b1-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="f91b1-134">在 [`@code`][1] 块中，通过用于处理事件或定义其他组件逻辑的方法指定组件状态（属性、字段）。</span><span class="sxs-lookup"><span data-stu-id="f91b1-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="f91b1-135">允许多个 [`@code`][1] 块。</span><span class="sxs-lookup"><span data-stu-id="f91b1-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="f91b1-136">可以使用以 `@` 开头的 C# 表达式将组件成员作为组件的呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="f91b1-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="f91b1-137">例如，通过为字段名称添加 `@` 前缀来呈现 C# 字段。</span><span class="sxs-lookup"><span data-stu-id="f91b1-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="f91b1-138">下面的示例计算并呈现：</span><span class="sxs-lookup"><span data-stu-id="f91b1-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="f91b1-139">`font-style` 的 CSS 属性值的 `headingFontStyle`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="f91b1-140">`<h1>` 元素内容的 `headingText`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="f91b1-141">最初呈现组件后，组件会为响应事件而重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="f91b1-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="f91b1-142">然后 Blazor 将新呈现树与前一个呈现树进行比较，并对浏览器文档对象模型 (DOM) 应用任何修改。</span><span class="sxs-lookup"><span data-stu-id="f91b1-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="f91b1-143">组件是普通 C# 类，可以放置在项目中的任何位置。</span><span class="sxs-lookup"><span data-stu-id="f91b1-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="f91b1-144">生成网页的组件通常位于 `Pages` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f91b1-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="f91b1-145">非页面组件通常放置在 `Shared` 文件夹或添加到项目的自定义文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f91b1-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="f91b1-146">命名空间</span><span class="sxs-lookup"><span data-stu-id="f91b1-146">Namespaces</span></span>

<span data-ttu-id="f91b1-147">通常，组件的命名空间是从应用的根命名空间和该组件在应用内的位置（文件夹）派生而来的。</span><span class="sxs-lookup"><span data-stu-id="f91b1-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="f91b1-148">如果应用的根命名空间是 `BlazorSample`，并且 `Counter` 组件位于 `Pages` 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="f91b1-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="f91b1-149">`Counter` 组件的命名空间为 `BlazorSample.Pages`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="f91b1-150">组件的完全限定类型名称为 `BlazorSample.Pages.Counter`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="f91b1-151">对于保存组件的自定义文件夹，将 [`@using`][2] 指令添加到父组件或应用的 `_Imports.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="f91b1-152">下面的示例提供 `Components` 文件夹中的组件：</span><span class="sxs-lookup"><span data-stu-id="f91b1-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="f91b1-153">还可以使用其完全限定的名称来引用组件，而不需要 [`@using`][2] 指令：</span><span class="sxs-lookup"><span data-stu-id="f91b1-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="f91b1-154">使用 Razor 创建的组件的命名空间基于（按优先级顺序）：</span><span class="sxs-lookup"><span data-stu-id="f91b1-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="f91b1-155">Razor 文件 (`.razor`) 标记 (`@namespace BlazorSample.MyNamespace`) 中的 [`@namespace`][8] 指定内容。</span><span class="sxs-lookup"><span data-stu-id="f91b1-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="f91b1-156">项目文件 (`<RootNamespace>BlazorSample</RootNamespace>`) 中项目的 `RootNamespace`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="f91b1-157">项目名称，取自项目文件的文件名 (`.csproj`)，以及从项目根到组件的路径。</span><span class="sxs-lookup"><span data-stu-id="f91b1-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="f91b1-158">例如，框架将 `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) 解析为命名空间 `BlazorSample.Pages`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="f91b1-159">组件遵循 C# 名称绑定规则。</span><span class="sxs-lookup"><span data-stu-id="f91b1-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="f91b1-160">对于本示例中的 `Index` 组件，范围内的组件是所有组件：</span><span class="sxs-lookup"><span data-stu-id="f91b1-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="f91b1-161">在同一文件夹 `Pages` 中。</span><span class="sxs-lookup"><span data-stu-id="f91b1-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="f91b1-162">未显式指定其他命名空间的项目根中的组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="f91b1-163">不支持 `global::` 限定。</span><span class="sxs-lookup"><span data-stu-id="f91b1-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="f91b1-164">不支持导入具有别名 [`using`](/dotnet/csharp/language-reference/keywords/using-statement) 语句的组件（例如，`@using Foo = Bar`）。</span><span class="sxs-lookup"><span data-stu-id="f91b1-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="f91b1-165">不支持部分限定名称。</span><span class="sxs-lookup"><span data-stu-id="f91b1-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="f91b1-166">例如，不支持使用 `<Shared.NavMenu></Shared.NavMenu>` 添加 `@using BlazorSample` 和引用 `NavMenu` 组件 (`NavMenu.razor`)。</span><span class="sxs-lookup"><span data-stu-id="f91b1-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="f91b1-167">分部类支持</span><span class="sxs-lookup"><span data-stu-id="f91b1-167">Partial class support</span></span>

Razor<span data-ttu-id="f91b1-168"> 组件作为分部类生成。</span><span class="sxs-lookup"><span data-stu-id="f91b1-168"> components are generated as partial classes.</span></span> <span data-ttu-id="f91b1-169">使用以下方法之一创建 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="f91b1-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="f91b1-170">在 [`@code`][1] 块中使用单个文件中的 HTML 标记和 Razor 代码定义 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="f91b1-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="f91b1-171"> 模板使用此方法来定义其 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-171"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="f91b1-172">C# 代码位于定义为分部类的代码隐藏文件中。</span><span class="sxs-lookup"><span data-stu-id="f91b1-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="f91b1-173">下面的示例显示了从 Blazor 模板生成的应用中具有 [`@code`][1] 块的默认 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="f91b1-174">HTML 标记、Razor 代码和 C# 代码位于同一个文件中：</span><span class="sxs-lookup"><span data-stu-id="f91b1-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="f91b1-175">`Pages/Counter.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-175">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="f91b1-176">还可以使用具有分部类的代码隐藏文件创建 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="f91b1-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="f91b1-177">`Pages/Counter.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="f91b1-178">`Counter.razor.cs`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="f91b1-179">根据需要将任何所需的命名空间添加到分部类文件中。</span><span class="sxs-lookup"><span data-stu-id="f91b1-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="f91b1-180">Razor 组件使用的典型命名空间包括：</span><span class="sxs-lookup"><span data-stu-id="f91b1-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

### <a name="specify-a-base-class"></a><span data-ttu-id="f91b1-181">指定基类</span><span class="sxs-lookup"><span data-stu-id="f91b1-181">Specify a base class</span></span>

<span data-ttu-id="f91b1-182">[`@inherits`][6] 指令可用于指定组件的基类。</span><span class="sxs-lookup"><span data-stu-id="f91b1-182">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="f91b1-183">下面的示例演示组件如何继承基类 `BlazorRocksBase` 以提供组件的属性和方法。</span><span class="sxs-lookup"><span data-stu-id="f91b1-183">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="f91b1-184">基类应派生自 <xref:Microsoft.AspNetCore.Components.ComponentBase>。</span><span class="sxs-lookup"><span data-stu-id="f91b1-184">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="f91b1-185">`Pages/BlazorRocks.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-185">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="f91b1-186">`BlazorRocksBase.cs`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-186">`BlazorRocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="f91b1-187">使用组件</span><span class="sxs-lookup"><span data-stu-id="f91b1-187">Use components</span></span>

<span data-ttu-id="f91b1-188">通过使用 HTML 元素语法声明组件，组件可以包含其他组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-188">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="f91b1-189">使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。</span><span class="sxs-lookup"><span data-stu-id="f91b1-189">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="f91b1-190">`Pages/Index.razor` 中的以下标记呈现了 `HeadingComponent` 实例：</span><span class="sxs-lookup"><span data-stu-id="f91b1-190">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="f91b1-191">`Components/HeadingComponent.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-191">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="f91b1-192">如果某个组件包含一个 HTML 元素，该元素的大写首字母与组件名称不匹配，则会发出警告，指示该元素名称异常。</span><span class="sxs-lookup"><span data-stu-id="f91b1-192">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="f91b1-193">为组件的命名空间添加 [`@using`][2] 指令使组件可用，从而解决此警告。</span><span class="sxs-lookup"><span data-stu-id="f91b1-193">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="f91b1-194">参数</span><span class="sxs-lookup"><span data-stu-id="f91b1-194">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="f91b1-195">路由参数</span><span class="sxs-lookup"><span data-stu-id="f91b1-195">Route parameters</span></span>

<span data-ttu-id="f91b1-196">组件可以接收来自 [`@page`][9] 指令所提供的路由模板的路由参数。</span><span class="sxs-lookup"><span data-stu-id="f91b1-196">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="f91b1-197">路由器使用路由参数来填充相应的组件参数。</span><span class="sxs-lookup"><span data-stu-id="f91b1-197">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="f91b1-198">`Pages/RouteParameter.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-198">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="f91b1-199">不支持可选参数，因此在前面的示例中应用了两个 [`@page`][9] 指令。</span><span class="sxs-lookup"><span data-stu-id="f91b1-199">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="f91b1-200">第一个指令允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-200">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="f91b1-201">第二个 [`@page`][9] 指令会接收 `{text}` 路由参数，并将值赋予 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="f91b1-201">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="f91b1-202">Razor 组件 (`.razor`) 不支持 Catch-all 参数语法 (`*`/`**`)，该语法捕获跨多个文件夹边界的路径。</span><span class="sxs-lookup"><span data-stu-id="f91b1-202">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (`.razor`).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="f91b1-203">组件参数</span><span class="sxs-lookup"><span data-stu-id="f91b1-203">Component parameters</span></span>

<span data-ttu-id="f91b1-204">组件可以有组件参数，这些参数是使用组件类中包含 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 特性的公共属性定义的。</span><span class="sxs-lookup"><span data-stu-id="f91b1-204">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="f91b1-205">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="f91b1-205">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="f91b1-206">`Components/ChildComponent.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-206">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="f91b1-207">在示例应用的以下示例中，`ParentComponent` 设置 `ChildComponent` 的 `Title` 属性的值。</span><span class="sxs-lookup"><span data-stu-id="f91b1-207">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="f91b1-208">`Pages/ParentComponent.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-208">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="f91b1-209">请勿创建会写入其自己的组件参数的组件，而是使用私有字段。</span><span class="sxs-lookup"><span data-stu-id="f91b1-209">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="f91b1-210">有关详细信息，请参阅[请勿创建会写入其自己的组参数属性的组件](#dont-create-components-that-write-to-their-own-parameter-properties)部分。</span><span class="sxs-lookup"><span data-stu-id="f91b1-210">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="f91b1-211">子内容</span><span class="sxs-lookup"><span data-stu-id="f91b1-211">Child content</span></span>

<span data-ttu-id="f91b1-212">组件可以设置另一个组件的内容。</span><span class="sxs-lookup"><span data-stu-id="f91b1-212">Components can set the content of another component.</span></span> <span data-ttu-id="f91b1-213">分配组件提供指定接收组件的标记之间的内容。</span><span class="sxs-lookup"><span data-stu-id="f91b1-213">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="f91b1-214">在下面的示例中，`ChildComponent` 具有一个表示 <xref:Microsoft.AspNetCore.Components.RenderFragment>（表示要呈现的 UI 段）的 `ChildContent` 属性。</span><span class="sxs-lookup"><span data-stu-id="f91b1-214">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="f91b1-215">`ChildContent` 的值放置在应呈现内容的组件标记中。</span><span class="sxs-lookup"><span data-stu-id="f91b1-215">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="f91b1-216">`ChildContent` 的值是从父组件接收的，并呈现在启动面板的 `panel-body` 中。</span><span class="sxs-lookup"><span data-stu-id="f91b1-216">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="f91b1-217">`Components/ChildComponent.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-217">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="f91b1-218">必须按约定将接收 <xref:Microsoft.AspNetCore.Components.RenderFragment> 内容的属性命名为 `ChildContent`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-218">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="f91b1-219">示例应用中的 `ParentComponent` 可以通过将内容置于 `<ChildComponent>` 标记中，提供用于呈现 `ChildComponent` 的内容。</span><span class="sxs-lookup"><span data-stu-id="f91b1-219">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="f91b1-220">`Pages/ParentComponent.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-220">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="f91b1-221">属性展开和任意参数</span><span class="sxs-lookup"><span data-stu-id="f91b1-221">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="f91b1-222">除了组件的声明参数外，组件还可以捕获和呈现其他属性。</span><span class="sxs-lookup"><span data-stu-id="f91b1-222">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="f91b1-223">其他属性可以在字典中捕获，然后在使用 [`@attributes`][3] Razor 指令呈现组件时，将其展开到元素上。</span><span class="sxs-lookup"><span data-stu-id="f91b1-223">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="f91b1-224">此方案在定义生成支持各种自定义项的标记元素的组件时非常有用。</span><span class="sxs-lookup"><span data-stu-id="f91b1-224">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="f91b1-225">例如，为支持多个参数的 `<input>` 单独定义属性可能比较繁琐。</span><span class="sxs-lookup"><span data-stu-id="f91b1-225">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="f91b1-226">在下面的示例中，第一个 `<input>` 元素 (`id="useIndividualParams"`) 使用单个组件参数，第二个 `<input>` 元素 (`id="useAttributesDict"`) 使用属性展开：</span><span class="sxs-lookup"><span data-stu-id="f91b1-226">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="f91b1-227">参数的类型必须使用字符串键实现 `IEnumerable<KeyValuePair<string, object>>`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-227">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="f91b1-228">在此方案中，也可以选择使用 `IReadOnlyDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-228">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="f91b1-229">使用这两种方法呈现的 `<input>` 元素相同：</span><span class="sxs-lookup"><span data-stu-id="f91b1-229">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="f91b1-230">若要接受任意特性，请使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 特性定义组件参数，并将 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-230">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="f91b1-231">借助 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 上的 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 属性，参数可以匹配所有不匹配其他任何参数的特性。</span><span class="sxs-lookup"><span data-stu-id="f91b1-231">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="f91b1-232">组件只能使用 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 定义单个参数。</span><span class="sxs-lookup"><span data-stu-id="f91b1-232">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="f91b1-233">与 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 一起使用的属性类型必须可以使用字符串键从 `Dictionary<string, object>` 中分配。</span><span class="sxs-lookup"><span data-stu-id="f91b1-233">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="f91b1-234">`IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>` 也是此方案中的选项。</span><span class="sxs-lookup"><span data-stu-id="f91b1-234">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="f91b1-235">相对于元素特性位置的 [`@attributes`][3] 位置很重要。</span><span class="sxs-lookup"><span data-stu-id="f91b1-235">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="f91b1-236">在元素上展开 [`@attributes`][3] 时，将从右到左（从最后一个到第一个）处理特性。</span><span class="sxs-lookup"><span data-stu-id="f91b1-236">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="f91b1-237">请考虑以下使用 `Child` 组件的组件示例：</span><span class="sxs-lookup"><span data-stu-id="f91b1-237">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="f91b1-238">`ParentComponent.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-238">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="f91b1-239">`ChildComponent.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-239">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="f91b1-240">`Child` 组件的 `extra` 属性设置为 [`@attributes`][3] 右侧。</span><span class="sxs-lookup"><span data-stu-id="f91b1-240">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="f91b1-241">通过附加特性传递时，`Parent` 组件的呈现的 `<div>` 包含 `extra="5"`，因为特性是从右到左（从最后一个到第一个）处理的：</span><span class="sxs-lookup"><span data-stu-id="f91b1-241">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="f91b1-242">在下面的示例中，`extra` 和 [`@attributes`][3] 的顺序在 `Child` 组件的 `<div>` 中反转：</span><span class="sxs-lookup"><span data-stu-id="f91b1-242">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="f91b1-243">`ParentComponent.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-243">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="f91b1-244">`ChildComponent.razor`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-244">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="f91b1-245">通过附加属性传递时，`Parent` 组件中呈现的 `<div>` 包含 `extra="10"`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-245">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="f91b1-246">捕获对组件的引用</span><span class="sxs-lookup"><span data-stu-id="f91b1-246">Capture references to components</span></span>

<span data-ttu-id="f91b1-247">组件引用提供了一种引用组件实例的方法，以便可以向该实例发出命令，例如 `Show` 或 `Reset`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-247">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="f91b1-248">若要捕获组件引用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f91b1-248">To capture a component reference:</span></span>

* <span data-ttu-id="f91b1-249">向子组件添加 [`@ref`][4] 特性。</span><span class="sxs-lookup"><span data-stu-id="f91b1-249">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="f91b1-250">定义与子组件类型相同的字段。</span><span class="sxs-lookup"><span data-stu-id="f91b1-250">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="f91b1-251">呈现组件时，将用 `MyLoginDialog` 子组件实例填充 `loginDialog` 字段。</span><span class="sxs-lookup"><span data-stu-id="f91b1-251">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="f91b1-252">然后，可以在组件实例上调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="f91b1-252">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f91b1-253">仅在呈现组件后填充 `loginDialog` 变量，其输出包含 `MyLoginDialog` 元素。</span><span class="sxs-lookup"><span data-stu-id="f91b1-253">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="f91b1-254">在呈现组件之前，没有任何可引用的内容。</span><span class="sxs-lookup"><span data-stu-id="f91b1-254">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="f91b1-255">若要在组件完成呈现后操作组件引用，请使用 [`OnAfterRenderAsync` 或 `OnAfterRender` 方法](xref:blazor/components/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="f91b1-255">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="f91b1-256">若要结合使用事件处理程序和引用变量，请使用 Lambda 表达式，或在 [`OnAfterRenderAsync` 或 `OnAfterRender` 方法](xref:blazor/components/lifecycle#after-component-render)中分配事件处理程序委托。</span><span class="sxs-lookup"><span data-stu-id="f91b1-256">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="f91b1-257">这可确保在分配事件处理程序之前先分配引用变量。</span><span class="sxs-lookup"><span data-stu-id="f91b1-257">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="f91b1-258">要引用循环中的组件，请参阅[捕获对多个相似子组件的引用 (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358)。</span><span class="sxs-lookup"><span data-stu-id="f91b1-258">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="f91b1-259">尽管捕获组件引用使用与[捕获元素引用](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)类似的语法，但它不是 JavaScript 互操作功能。</span><span class="sxs-lookup"><span data-stu-id="f91b1-259">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="f91b1-260">组件引用不会传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="f91b1-260">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="f91b1-261">组件引用只在 .NET 代码中使用。</span><span class="sxs-lookup"><span data-stu-id="f91b1-261">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="f91b1-262">不要使用组件引用来改变子组件的状态。</span><span class="sxs-lookup"><span data-stu-id="f91b1-262">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="f91b1-263">请改用常规声明性参数将数据传递给子组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-263">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="f91b1-264">使用常规声明性参数使子组件在正确的时间自动重新呈现。</span><span class="sxs-lookup"><span data-stu-id="f91b1-264">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="f91b1-265">同步上下文</span><span class="sxs-lookup"><span data-stu-id="f91b1-265">Synchronization context</span></span>

Blazor<span data-ttu-id="f91b1-266"> 使用同步上下文 (<xref:System.Threading.SynchronizationContext>) 来强制执行单个逻辑线程。</span><span class="sxs-lookup"><span data-stu-id="f91b1-266"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="f91b1-267">组件的[生命周期方法](xref:blazor/components/lifecycle)和 Blazor 引发的任何事件回调都在此同步上下文上执行。</span><span class="sxs-lookup"><span data-stu-id="f91b1-267">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor Server<span data-ttu-id="f91b1-268">的同步上下文尝试模拟单线程环境，使其与浏览器中的单线程 WebAssembly 模型紧密匹配。</span><span class="sxs-lookup"><span data-stu-id="f91b1-268">'s synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="f91b1-269">在任意给定的时间点，工作只在一个线程上执行，从而造成单个逻辑线程的印象。</span><span class="sxs-lookup"><span data-stu-id="f91b1-269">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="f91b1-270">不会同时执行两个操作。</span><span class="sxs-lookup"><span data-stu-id="f91b1-270">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="f91b1-271">避免阻止线程的调用</span><span class="sxs-lookup"><span data-stu-id="f91b1-271">Avoid thread-blocking calls</span></span>

<span data-ttu-id="f91b1-272">通常，不要调用以下方法。</span><span class="sxs-lookup"><span data-stu-id="f91b1-272">Generally, don't call the following methods.</span></span> <span data-ttu-id="f91b1-273">以下方法阻止线程，进而阻止应用继续工作，直到基础 <xref:System.Threading.Tasks.Task> 完成：</span><span class="sxs-lookup"><span data-stu-id="f91b1-273">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="f91b1-274">在外部调用组件方法以更新状态</span><span class="sxs-lookup"><span data-stu-id="f91b1-274">Invoke component methods externally to update state</span></span>

<span data-ttu-id="f91b1-275">如果组件必须根据外部事件（如计时器或其他通知）进行更新，请使用 `InvokeAsync` 方法来调度到 Blazor 的同步上下文。</span><span class="sxs-lookup"><span data-stu-id="f91b1-275">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="f91b1-276">例如，假设有一个可通知任何侦听组件更新状态的通告程序服务：</span><span class="sxs-lookup"><span data-stu-id="f91b1-276">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="f91b1-277">注册 `NotifierService`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-277">Register the `NotifierService`:</span></span>

* <span data-ttu-id="f91b1-278">在 Blazor WebAssembly 中，在 `Program.Main` 中将服务注册为单一实例：</span><span class="sxs-lookup"><span data-stu-id="f91b1-278">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="f91b1-279">在 Blazor Server 中，在 `Startup.ConfigureServices` 中将服务注册为有作用域：</span><span class="sxs-lookup"><span data-stu-id="f91b1-279">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="f91b1-280">使用 `NotifierService` 更新组件：</span><span class="sxs-lookup"><span data-stu-id="f91b1-280">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="f91b1-281">在前面的示例中，`NotifierService` 在 Blazor 的同步上下文之外调用组件的 `OnNotify` 方法。</span><span class="sxs-lookup"><span data-stu-id="f91b1-281">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="f91b1-282">`InvokeAsync` 用于切换到正确的上下文，并将呈现排入队列。</span><span class="sxs-lookup"><span data-stu-id="f91b1-282">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="f91b1-283">使用 \@ 键控制是否保留元素和组件</span><span class="sxs-lookup"><span data-stu-id="f91b1-283">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="f91b1-284">在呈现元素或组件的列表并且元素或组件随后发生更改时，Blazor 的比较算法必须决定之前的元素或组件中有哪些可以保留，以及模型对象应如何映射到这些元素或组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-284">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="f91b1-285">通常，此过程是自动的，可以忽略，但在某些情况下，可能需要控制该过程。</span><span class="sxs-lookup"><span data-stu-id="f91b1-285">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="f91b1-286">请看下面的示例：</span><span class="sxs-lookup"><span data-stu-id="f91b1-286">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="f91b1-287">`People` 集合的内容可能会随插入、删除或重新排序的条目而更改。</span><span class="sxs-lookup"><span data-stu-id="f91b1-287">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="f91b1-288">当组件重新呈现时，`<DetailsEditor>` 组件可能会更改以接收不同的 `Details` 参数值。</span><span class="sxs-lookup"><span data-stu-id="f91b1-288">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="f91b1-289">这可能导致重新呈现比预期更复杂。</span><span class="sxs-lookup"><span data-stu-id="f91b1-289">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="f91b1-290">在某些情况下，重新呈现可能会导致可见行为差异，如失去元素焦点。</span><span class="sxs-lookup"><span data-stu-id="f91b1-290">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="f91b1-291">可通过 [`@key`][5] 指令属性来控制映射过程。</span><span class="sxs-lookup"><span data-stu-id="f91b1-291">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="f91b1-292">[`@key`][5] 使比较算法保证基于键的值保留元素或组件：</span><span class="sxs-lookup"><span data-stu-id="f91b1-292">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="f91b1-293">当 `People` 集合发生更改时，比较算法会保留 `<DetailsEditor>` 实例与 `person` 实例之间的关联：</span><span class="sxs-lookup"><span data-stu-id="f91b1-293">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="f91b1-294">如果从 `People` 列表中删除 `Person`，则仅从 UI 中删除相应的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="f91b1-294">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="f91b1-295">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="f91b1-295">Other instances are left unchanged.</span></span>
* <span data-ttu-id="f91b1-296">如果在列表中的某个位置插入 `Person`，则会在相应位置插入一个新的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="f91b1-296">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="f91b1-297">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="f91b1-297">Other instances are left unchanged.</span></span>
* <span data-ttu-id="f91b1-298">如果对 `Person` 项进行重新排序，则保留相应的 `<DetailsEditor>` 实例，并在 UI 中重新排序。</span><span class="sxs-lookup"><span data-stu-id="f91b1-298">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="f91b1-299">在某些场景中，使用 [`@key`][5] 可最大程度降低重新呈现的复杂性，并避免在 DOM 的有状态部分发生变化时出现潜在问题，如焦点位置。</span><span class="sxs-lookup"><span data-stu-id="f91b1-299">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f91b1-300">对于每个容器元素或组件而言，键是本地的。</span><span class="sxs-lookup"><span data-stu-id="f91b1-300">Keys are local to each container element or component.</span></span> <span data-ttu-id="f91b1-301">不会在整个文档中全局地比较键。</span><span class="sxs-lookup"><span data-stu-id="f91b1-301">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="f91b1-302">何时使用 \@key</span><span class="sxs-lookup"><span data-stu-id="f91b1-302">When to use \@key</span></span>

<span data-ttu-id="f91b1-303">通常，每当列表呈现（例如，在 [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 块中）且存在合适的值来定义 [`@key`][5] 时，最好使用 [`@key`][5]。</span><span class="sxs-lookup"><span data-stu-id="f91b1-303">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="f91b1-304">还可以使用 [`@key`][5] 来防止 Blazor 在对象发生更改时保留元素或组件子树：</span><span class="sxs-lookup"><span data-stu-id="f91b1-304">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="f91b1-305">如果 `@currentPerson` 更改，则 [`@key`][5] 属性指令强制 Blazor 放弃整个 `<div>` 及其子代，并利用新的元素和组件在 UI 中重新生成子树。</span><span class="sxs-lookup"><span data-stu-id="f91b1-305">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="f91b1-306">如果需要确保在 `@currentPerson` 更改时不保留 UI 状态，这会很有用。</span><span class="sxs-lookup"><span data-stu-id="f91b1-306">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="f91b1-307">何时不使用 \@key</span><span class="sxs-lookup"><span data-stu-id="f91b1-307">When not to use \@key</span></span>

<span data-ttu-id="f91b1-308">与 [`@key`][5] 进行比较时，会产生性能成本。</span><span class="sxs-lookup"><span data-stu-id="f91b1-308">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="f91b1-309">性能成本不是很大，但仅在控制元素或组件保留规则对应用有利的情况下才指定 [`@key`][5]。</span><span class="sxs-lookup"><span data-stu-id="f91b1-309">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="f91b1-310">即使未使用 [`@key`][5]，Blazor 也会尽可能地保留子元素和组件实例。</span><span class="sxs-lookup"><span data-stu-id="f91b1-310">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="f91b1-311">使用 [`@key`][5] 的唯一优点是控制如何将模型实例映射到保留的组件实例，而不是选择映射的比较算法。</span><span class="sxs-lookup"><span data-stu-id="f91b1-311">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="f91b1-312">\@key 使用哪些值</span><span class="sxs-lookup"><span data-stu-id="f91b1-312">What values to use for \@key</span></span>

<span data-ttu-id="f91b1-313">通常，为 [`@key`][5] 提供以下类型之一的值：</span><span class="sxs-lookup"><span data-stu-id="f91b1-313">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="f91b1-314">模型对象实例（例如，先前示例中的 `Person` 实例）。</span><span class="sxs-lookup"><span data-stu-id="f91b1-314">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="f91b1-315">这可确保基于对象引用相等性的保留。</span><span class="sxs-lookup"><span data-stu-id="f91b1-315">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="f91b1-316">唯一标识符（例如，`int`、`string` 或 `Guid` 类型的主键值）。</span><span class="sxs-lookup"><span data-stu-id="f91b1-316">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="f91b1-317">确保用于 [`@key`][5] 的值不冲突。</span><span class="sxs-lookup"><span data-stu-id="f91b1-317">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="f91b1-318">如果在同一父元素内检测到冲突值，则 Blazor 引发异常，因为它无法明确地将旧元素或组件映射到新元素或组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-318">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="f91b1-319">仅使用非重复值，例如对象实例或主键值。</span><span class="sxs-lookup"><span data-stu-id="f91b1-319">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="f91b1-320">请勿创建会写入其自己的组参数属性的组件</span><span class="sxs-lookup"><span data-stu-id="f91b1-320">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="f91b1-321">在以下情况中，会重写参数：</span><span class="sxs-lookup"><span data-stu-id="f91b1-321">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="f91b1-322">子组件的内容使用 <xref:Microsoft.AspNetCore.Components.RenderFragment> 进行呈现。</span><span class="sxs-lookup"><span data-stu-id="f91b1-322">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="f91b1-323"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在父组件中调用。</span><span class="sxs-lookup"><span data-stu-id="f91b1-323"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="f91b1-324">由于在调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 且向子组件提供新的参数值时，会重新呈现父组件，因此将重置参数。</span><span class="sxs-lookup"><span data-stu-id="f91b1-324">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="f91b1-325">请考虑使用以下 `Expander` 组件，它们会：</span><span class="sxs-lookup"><span data-stu-id="f91b1-325">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="f91b1-326">呈现子内容。</span><span class="sxs-lookup"><span data-stu-id="f91b1-326">Renders child content.</span></span>
* <span data-ttu-id="f91b1-327">切换以使用组件参数显示子内容。</span><span class="sxs-lookup"><span data-stu-id="f91b1-327">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle" class="card text-white bg-success mb-3">
    <div class="card-body">
        <div class="panel-heading">
            <h2>Toggle (Expanded = @Expanded)</h2>
        </div>

        @if (Expanded)
        {
            <div class="card-text">
                @ChildContent
            </div>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="f91b1-328">`Expander` 组件会添加到可调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>的父组件中：</span><span class="sxs-lookup"><span data-stu-id="f91b1-328">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="f91b1-329">最初，在切换 `Expanded` 属性时，`Expander` 组件独立地作出行为。</span><span class="sxs-lookup"><span data-stu-id="f91b1-329">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="f91b1-330">子组件会按预期方式维护其状态。</span><span class="sxs-lookup"><span data-stu-id="f91b1-330">The child components maintain their states as expected.</span></span> <span data-ttu-id="f91b1-331">在父组件中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 时，第一个子组件的 `Expanded` 会重新重置为它的初始值 (`true`)。</span><span class="sxs-lookup"><span data-stu-id="f91b1-331">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="f91b1-332">第二个 `Expander` 组件的 `Expanded` 值不会重置，因为第二个组件中没有呈现任何子内容。</span><span class="sxs-lookup"><span data-stu-id="f91b1-332">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="f91b1-333">要维持在前述情况中的状态，请在 `Expander` 组件中使用私有字段来保留它的切换状态。</span><span class="sxs-lookup"><span data-stu-id="f91b1-333">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="f91b1-334">以下经修定的 `Expander` 组件：</span><span class="sxs-lookup"><span data-stu-id="f91b1-334">The following revised `Expander` component:</span></span>

* <span data-ttu-id="f91b1-335">接受父项中的 `Expanded` 组件参数值。</span><span class="sxs-lookup"><span data-stu-id="f91b1-335">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="f91b1-336">将组件参数值分配给 [OnInitialized 事件](xref:blazor/components/lifecycle#component-initialization-methods)中的私有字段 (`expanded`)。</span><span class="sxs-lookup"><span data-stu-id="f91b1-336">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="f91b1-337">使用私有字段来维持它的内部切换状态。</span><span class="sxs-lookup"><span data-stu-id="f91b1-337">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle" class="card text-white bg-success mb-3">
    <div class="card-body">
        <div class="panel-heading">
            <h2>Toggle (Expanded = @expanded)</h2>
        </div>

        @if (Expanded)
        {
            <div class="card-text">
                @ChildContent
            </div>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="apply-an-attribute"></a><span data-ttu-id="f91b1-338">应用属性</span><span class="sxs-lookup"><span data-stu-id="f91b1-338">Apply an attribute</span></span>

<span data-ttu-id="f91b1-339">可以通过 [`@attribute`][7] 指令在 Razor 组件中应用属性。</span><span class="sxs-lookup"><span data-stu-id="f91b1-339">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="f91b1-340">下面的示例将 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性应用于组件类：</span><span class="sxs-lookup"><span data-stu-id="f91b1-340">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="f91b1-341">条件 HTML 元素属性</span><span class="sxs-lookup"><span data-stu-id="f91b1-341">Conditional HTML element attributes</span></span>

<span data-ttu-id="f91b1-342">HTML 元素属性基于 .NET 值有条件地呈现。</span><span class="sxs-lookup"><span data-stu-id="f91b1-342">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="f91b1-343">如果值为 `false` 或 `null`，则不会呈现属性。</span><span class="sxs-lookup"><span data-stu-id="f91b1-343">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="f91b1-344">如果值为 `true`，则以最小化呈现属性。</span><span class="sxs-lookup"><span data-stu-id="f91b1-344">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="f91b1-345">在下面的示例中，`IsCompleted` 确定是否在元素的标记中呈现 `checked`：</span><span class="sxs-lookup"><span data-stu-id="f91b1-345">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="f91b1-346">如果 `IsCompleted` 为 `true`，则复选框呈现为：</span><span class="sxs-lookup"><span data-stu-id="f91b1-346">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="f91b1-347">如果 `IsCompleted` 为 `false`，则复选框呈现为：</span><span class="sxs-lookup"><span data-stu-id="f91b1-347">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="f91b1-348">有关详细信息，请参阅 <xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="f91b1-348">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="f91b1-349">.NET 类型为 `bool` 时，某些 HTML 属性（如 [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)）无法正常运行。</span><span class="sxs-lookup"><span data-stu-id="f91b1-349">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="f91b1-350">在这些情况下，请使用 `string` 类型，而不是 `bool`。</span><span class="sxs-lookup"><span data-stu-id="f91b1-350">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="f91b1-351">原始 HTML</span><span class="sxs-lookup"><span data-stu-id="f91b1-351">Raw HTML</span></span>

<span data-ttu-id="f91b1-352">通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文字文本。</span><span class="sxs-lookup"><span data-stu-id="f91b1-352">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="f91b1-353">若要呈现原始 HTML，请将 HTML 内容包装在 `MarkupString` 值中。</span><span class="sxs-lookup"><span data-stu-id="f91b1-353">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="f91b1-354">将该值分析为 HTML 或 SVG，并插入到 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="f91b1-354">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="f91b1-355">呈现从任何不受信任的源构造的原始 HTML 存在安全风险，应避免！</span><span class="sxs-lookup"><span data-stu-id="f91b1-355">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="f91b1-356">下面的示例演示如何使用 `MarkupString` 类型向组件的呈现输出添加静态 HTML 内容块：</span><span class="sxs-lookup"><span data-stu-id="f91b1-356">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a>Razor<span data-ttu-id="f91b1-357"> 模板</span><span class="sxs-lookup"><span data-stu-id="f91b1-357"> templates</span></span>

<span data-ttu-id="f91b1-358">可以使用 Razor 模板语法来定义呈现片段。</span><span class="sxs-lookup"><span data-stu-id="f91b1-358">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="f91b1-359"> 模板是一种定义 UI 代码片段的方法，请使用以下格式：</span><span class="sxs-lookup"><span data-stu-id="f91b1-359"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="f91b1-360">下面的示例演示如何在组件中指定 <xref:Microsoft.AspNetCore.Components.RenderFragment> 和 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 值并直接呈现模板。</span><span class="sxs-lookup"><span data-stu-id="f91b1-360">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="f91b1-361">还可以将呈现片段作为参数传递给[模板化组件](xref:blazor/components/templated-components)。</span><span class="sxs-lookup"><span data-stu-id="f91b1-361">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="f91b1-362">以上代码的呈现输出：</span><span class="sxs-lookup"><span data-stu-id="f91b1-362">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="f91b1-363">静态资产</span><span class="sxs-lookup"><span data-stu-id="f91b1-363">Static assets</span></span>

Blazor<span data-ttu-id="f91b1-364"> 遵循 ASP.NET Core 应用的约定，将静态资产放在项目的 [`web root (wwwroot)` 文件夹](xref:fundamentals/index#web-root)下。</span><span class="sxs-lookup"><span data-stu-id="f91b1-364"> follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="f91b1-365">使用基相对路径 (`/`) 来引用静态资产的 Web 根。</span><span class="sxs-lookup"><span data-stu-id="f91b1-365">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="f91b1-366">在下面的示例中，`logo.png` 实际位于 `{PROJECT ROOT}/wwwroot/images` 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="f91b1-366">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="f91b1-367"> 组件不支持波浪符斜杠表示法 (`~/`)。</span><span class="sxs-lookup"><span data-stu-id="f91b1-367"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="f91b1-368">有关设置应用基路径的信息，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="f91b1-368">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="f91b1-369">组件中不支持标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="f91b1-369">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="f91b1-370">Razor 组件（`.razor` 文件）不支持 [`Tag Helpers`](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="f91b1-370">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="f91b1-371">若要在 Blazor 中提供类似标记帮助程序的功能，请创建一个具有与标记帮助程序相同功能的组件，并改为使用该组件。</span><span class="sxs-lookup"><span data-stu-id="f91b1-371">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="f91b1-372">可缩放的向量图形 (SVG) 图像</span><span class="sxs-lookup"><span data-stu-id="f91b1-372">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="f91b1-373">由于 Blazor 呈现 HTML，因此通过 `<img>` 标记支持浏览器支持的图像，包括可缩放的矢量图形 (SVG) 图像(`.svg`)：</span><span class="sxs-lookup"><span data-stu-id="f91b1-373">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="f91b1-374">同样，样式表文件 (`.css`) 的 CSS 规则支持 SVG 图像：</span><span class="sxs-lookup"><span data-stu-id="f91b1-374">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="f91b1-375">但是，并非在所有情况下都支持内联的 SVG 标记。</span><span class="sxs-lookup"><span data-stu-id="f91b1-375">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="f91b1-376">如果将 `<svg>` 标记直接放入组件文件 (`.razor`)，则支持基本图像呈现，但很多高级场景尚不受支持。</span><span class="sxs-lookup"><span data-stu-id="f91b1-376">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="f91b1-377">例如，当前未遵循 `<use>` 标记，并且 [`@bind`][10] 不能与某些 SVG 标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="f91b1-377">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="f91b1-378">有关详细信息，请参阅 [Blazor (dotnet/aspnetcore #18271) 中的 SVG 支持](https://github.com/dotnet/aspnetcore/issues/18271)。</span><span class="sxs-lookup"><span data-stu-id="f91b1-378">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f91b1-379">其他资源</span><span class="sxs-lookup"><span data-stu-id="f91b1-379">Additional resources</span></span>

* <span data-ttu-id="f91b1-380"><xref:blazor/security/server/threat-mitigation>：包括有关如何生成必须应对资源耗尽的 Blazor Server应用的指南。</span><span class="sxs-lookup"><span data-stu-id="f91b1-380"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
