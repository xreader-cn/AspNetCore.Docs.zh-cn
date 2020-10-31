---
title: '创建和使用 ASP.NET Core :::no-loc(Razor)::: 组件'
author: guardrex
description: '了解如何创建和使用 :::no-loc(Razor)::: 组件，包括如何绑定到数据、处理事件和管理组件生命周期。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/index
ms.openlocfilehash: d30f40945a3b2799dfc2d9391bba37eee1bfdc18
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056265"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a><span data-ttu-id="9e88c-103">创建和使用 ASP.NET Core :::no-loc(Razor)::: 组件</span><span class="sxs-lookup"><span data-stu-id="9e88c-103">Create and use ASP.NET Core :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="9e88c-104">作者：[Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27) 和 [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="9e88c-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="9e88c-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9e88c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9e88c-106">:::no-loc(Blazor)::: 应用是使用组件构建的。</span><span class="sxs-lookup"><span data-stu-id="9e88c-106">:::no-loc(Blazor)::: apps are built using *components* .</span></span> <span data-ttu-id="9e88c-107">组件是自包含的用户界面 (UI) 块，例如页、对话框或窗体。</span><span class="sxs-lookup"><span data-stu-id="9e88c-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="9e88c-108">组件包含插入数据或响应 UI 事件所需的 HTML 标记和处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="9e88c-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="9e88c-109">组件非常灵活且轻量。</span><span class="sxs-lookup"><span data-stu-id="9e88c-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="9e88c-110">可在项目之间嵌套、重复使用和共享。</span><span class="sxs-lookup"><span data-stu-id="9e88c-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="9e88c-111">组件类</span><span class="sxs-lookup"><span data-stu-id="9e88c-111">Component classes</span></span>

<span data-ttu-id="9e88c-112">组件是使用 C# 和 HTML 标记的组合在 [:::no-loc(Razor):::](xref:mvc/views/razor) 组件文件 (`.razor`) 中实现的。</span><span class="sxs-lookup"><span data-stu-id="9e88c-112">Components are implemented in [:::no-loc(Razor):::](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="9e88c-113">:::no-loc(Blazor)::: 中的组件正式称为 :::no-loc(Razor)::: 组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-113">A component in :::no-loc(Blazor)::: is formally referred to as a *:::no-loc(Razor)::: component* .</span></span>

### <a name="no-locrazor-syntax"></a><span data-ttu-id="9e88c-114">:::no-loc(Razor)::: 语法</span><span class="sxs-lookup"><span data-stu-id="9e88c-114">:::no-loc(Razor)::: syntax</span></span>

<span data-ttu-id="9e88c-115">:::no-loc(Blazor)::: 应用中的 :::no-loc(Razor)::: 组件广泛使用 :::no-loc(Razor)::: 语法。</span><span class="sxs-lookup"><span data-stu-id="9e88c-115">:::no-loc(Razor)::: components in :::no-loc(Blazor)::: apps extensively use :::no-loc(Razor)::: syntax.</span></span> <span data-ttu-id="9e88c-116">如果你不熟悉 :::no-loc(Razor)::: 标记语言，建议先阅读 <xref:mvc/views/razor>，然后再继续。</span><span class="sxs-lookup"><span data-stu-id="9e88c-116">If you aren't familiar with the :::no-loc(Razor)::: markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="9e88c-117">访问 :::no-loc(Razor)::: 语法上的内容时，请特别注意以下各节：</span><span class="sxs-lookup"><span data-stu-id="9e88c-117">When accessing the content on :::no-loc(Razor)::: syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="9e88c-118">[指令](xref:mvc/views/razor#directives)：前缀为 `@` 的保留关键字，通常会更改组件标记的分析或运行方式。</span><span class="sxs-lookup"><span data-stu-id="9e88c-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="9e88c-119">[指令特性](xref:mvc/views/razor#directive-attributes)：前缀为 `@` 的保留关键字，通常会更改组件元素的分析或运行方式。</span><span class="sxs-lookup"><span data-stu-id="9e88c-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="9e88c-120">名称</span><span class="sxs-lookup"><span data-stu-id="9e88c-120">Names</span></span>

<span data-ttu-id="9e88c-121">组件的名称必须以大写字符开头。</span><span class="sxs-lookup"><span data-stu-id="9e88c-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="9e88c-122">例如，`MyCoolComponent.razor` 有效，而 `myCoolComponent.razor` 无效。</span><span class="sxs-lookup"><span data-stu-id="9e88c-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="9e88c-123">路由</span><span class="sxs-lookup"><span data-stu-id="9e88c-123">Routing</span></span>

<span data-ttu-id="9e88c-124">可以通过为应用中的每个可访问组件提供路由模板来实现 :::no-loc(Blazor)::: 中的路由。</span><span class="sxs-lookup"><span data-stu-id="9e88c-124">Routing in :::no-loc(Blazor)::: is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="9e88c-125">编译具有 [`@page`][9] 指令的 :::no-loc(Razor)::: 文件时，将为生成的类提供指定路由模板的 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>。</span><span class="sxs-lookup"><span data-stu-id="9e88c-125">When a :::no-loc(Razor)::: file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="9e88c-126">在运行时，路由器将使用 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 查找组件类，并呈现具有与请求的 URL 匹配的路由模板的任何组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="9e88c-127">有关详细信息，请参阅 <xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="9e88c-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="9e88c-128">标记</span><span class="sxs-lookup"><span data-stu-id="9e88c-128">Markup</span></span>

<span data-ttu-id="9e88c-129">使用 HTML 定义组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="9e88c-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="9e88c-130">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 *:::no-loc(Razor):::* 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="9e88c-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *:::no-loc(Razor):::* .</span></span> <span data-ttu-id="9e88c-131">在编译应用时，HTML 标记和 C# 呈现逻辑转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="9e88c-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="9e88c-132">生成的类的名称与文件名匹配。</span><span class="sxs-lookup"><span data-stu-id="9e88c-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="9e88c-133">组件类的成员在 [`@code`][1] 块中定义。</span><span class="sxs-lookup"><span data-stu-id="9e88c-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="9e88c-134">在 [`@code`][1] 块中，通过用于处理事件或定义其他组件逻辑的方法指定组件状态（属性、字段）。</span><span class="sxs-lookup"><span data-stu-id="9e88c-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="9e88c-135">允许多个 [`@code`][1] 块。</span><span class="sxs-lookup"><span data-stu-id="9e88c-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="9e88c-136">可以使用以 `@` 开头的 C# 表达式将组件成员作为组件的呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="9e88c-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="9e88c-137">例如，通过为字段名称添加 `@` 前缀来呈现 C# 字段。</span><span class="sxs-lookup"><span data-stu-id="9e88c-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="9e88c-138">下面的示例计算并呈现：</span><span class="sxs-lookup"><span data-stu-id="9e88c-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="9e88c-139">`font-style` 的 CSS 属性值的 `headingFontStyle`。</span><span class="sxs-lookup"><span data-stu-id="9e88c-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="9e88c-140">`<h1>` 元素内容的 `headingText`。</span><span class="sxs-lookup"><span data-stu-id="9e88c-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new :::no-loc(Blazor):::!";
}
```

<span data-ttu-id="9e88c-141">最初呈现组件后，组件会为响应事件而重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="9e88c-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="9e88c-142">然后 :::no-loc(Blazor)::: 将新呈现树与前一个呈现树进行比较，并对浏览器文档对象模型 (DOM) 应用任何修改。</span><span class="sxs-lookup"><span data-stu-id="9e88c-142">:::no-loc(Blazor)::: then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="9e88c-143">组件是普通 C# 类，可以放置在项目中的任何位置。</span><span class="sxs-lookup"><span data-stu-id="9e88c-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="9e88c-144">生成网页的组件通常位于 `Pages` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="9e88c-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="9e88c-145">非页面组件通常放置在 `Shared` 文件夹或添加到项目的自定义文件夹中。</span><span class="sxs-lookup"><span data-stu-id="9e88c-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="9e88c-146">命名空间</span><span class="sxs-lookup"><span data-stu-id="9e88c-146">Namespaces</span></span>

<span data-ttu-id="9e88c-147">通常，组件的命名空间是从应用的根命名空间和该组件在应用内的位置（文件夹）派生而来的。</span><span class="sxs-lookup"><span data-stu-id="9e88c-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="9e88c-148">如果应用的根命名空间是 `:::no-loc(Blazor):::Sample`，并且 `Counter` 组件位于 `Pages` 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="9e88c-148">If the app's root namespace is `:::no-loc(Blazor):::Sample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="9e88c-149">`Counter` 组件的命名空间为 `:::no-loc(Blazor):::Sample.Pages`。</span><span class="sxs-lookup"><span data-stu-id="9e88c-149">The `Counter` component's namespace is `:::no-loc(Blazor):::Sample.Pages`.</span></span>
* <span data-ttu-id="9e88c-150">组件的完全限定类型名称为 `:::no-loc(Blazor):::Sample.Pages.Counter`。</span><span class="sxs-lookup"><span data-stu-id="9e88c-150">The fully qualified type name of the component is `:::no-loc(Blazor):::Sample.Pages.Counter`.</span></span>

<span data-ttu-id="9e88c-151">对于保存组件的自定义文件夹，将 [`@using`][2] 指令添加到父组件或应用的 `_Imports.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="9e88c-152">下面的示例提供 `Components` 文件夹中的组件：</span><span class="sxs-lookup"><span data-stu-id="9e88c-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using :::no-loc(Blazor):::Sample.Components
```

<span data-ttu-id="9e88c-153">还可以使用其完全限定的名称来引用组件，而不需要 [`@using`][2] 指令：</span><span class="sxs-lookup"><span data-stu-id="9e88c-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<:::no-loc(Blazor):::Sample.Components.MyComponent />
```

<span data-ttu-id="9e88c-154">使用 :::no-loc(Razor)::: 创建的组件的命名空间基于（按优先级顺序）：</span><span class="sxs-lookup"><span data-stu-id="9e88c-154">The namespace of a component authored with :::no-loc(Razor)::: is based on (in priority order):</span></span>

* <span data-ttu-id="9e88c-155">:::no-loc(Razor)::: 文件 (`.razor`) 标记 (`@namespace :::no-loc(Blazor):::Sample.MyNamespace`) 中的 [`@namespace`][8] 指定内容。</span><span class="sxs-lookup"><span data-stu-id="9e88c-155">[`@namespace`][8] designation in :::no-loc(Razor)::: file (`.razor`) markup (`@namespace :::no-loc(Blazor):::Sample.MyNamespace`).</span></span>
* <span data-ttu-id="9e88c-156">项目文件 (`<RootNamespace>:::no-loc(Blazor):::Sample</RootNamespace>`) 中项目的 `RootNamespace`。</span><span class="sxs-lookup"><span data-stu-id="9e88c-156">The project's `RootNamespace` in the project file (`<RootNamespace>:::no-loc(Blazor):::Sample</RootNamespace>`).</span></span>
* <span data-ttu-id="9e88c-157">项目名称，取自项目文件的文件名 (`.csproj`)，以及从项目根到组件的路径。</span><span class="sxs-lookup"><span data-stu-id="9e88c-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="9e88c-158">例如，框架将 `{PROJECT ROOT}/Pages/Index.razor` (`:::no-loc(Blazor):::Sample.csproj`) 解析为命名空间 `:::no-loc(Blazor):::Sample.Pages`。</span><span class="sxs-lookup"><span data-stu-id="9e88c-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`:::no-loc(Blazor):::Sample.csproj`) to the namespace `:::no-loc(Blazor):::Sample.Pages`.</span></span> <span data-ttu-id="9e88c-159">组件遵循 C# 名称绑定规则。</span><span class="sxs-lookup"><span data-stu-id="9e88c-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="9e88c-160">对于本示例中的 `Index` 组件，范围内的组件是所有组件：</span><span class="sxs-lookup"><span data-stu-id="9e88c-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="9e88c-161">在同一文件夹 `Pages` 中。</span><span class="sxs-lookup"><span data-stu-id="9e88c-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="9e88c-162">未显式指定其他命名空间的项目根中的组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="9e88c-163">不支持 `global::` 限定。</span><span class="sxs-lookup"><span data-stu-id="9e88c-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="9e88c-164">不支持导入具有别名 [`using`](/dotnet/csharp/language-reference/keywords/using-statement) 语句的组件（例如，`@using Foo = Bar`）。</span><span class="sxs-lookup"><span data-stu-id="9e88c-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="9e88c-165">不支持部分限定名称。</span><span class="sxs-lookup"><span data-stu-id="9e88c-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="9e88c-166">例如，不支持使用 `<Shared.NavMenu></Shared.NavMenu>` 添加 `@using :::no-loc(Blazor):::Sample` 和引用 `NavMenu` 组件 (`NavMenu.razor`)。</span><span class="sxs-lookup"><span data-stu-id="9e88c-166">For example, adding `@using :::no-loc(Blazor):::Sample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="9e88c-167">分部类支持</span><span class="sxs-lookup"><span data-stu-id="9e88c-167">Partial class support</span></span>

<span data-ttu-id="9e88c-168">:::no-loc(Razor)::: 组件作为分部类生成。</span><span class="sxs-lookup"><span data-stu-id="9e88c-168">:::no-loc(Razor)::: components are generated as partial classes.</span></span> <span data-ttu-id="9e88c-169">使用以下方法之一创建 :::no-loc(Razor)::: 组件：</span><span class="sxs-lookup"><span data-stu-id="9e88c-169">:::no-loc(Razor)::: components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="9e88c-170">在 [`@code`][1] 块中使用单个文件中的 HTML 标记和 :::no-loc(Razor)::: 代码定义 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="9e88c-170">C# code is defined in an [`@code`][1] block with HTML markup and :::no-loc(Razor)::: code in a single file.</span></span> <span data-ttu-id="9e88c-171">:::no-loc(Blazor)::: 模板使用此方法来定义其 :::no-loc(Razor)::: 组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-171">:::no-loc(Blazor)::: templates define their :::no-loc(Razor)::: components using this approach.</span></span>
* <span data-ttu-id="9e88c-172">C# 代码位于定义为分部类的代码隐藏文件中。</span><span class="sxs-lookup"><span data-stu-id="9e88c-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="9e88c-173">下面的示例显示了从 :::no-loc(Blazor)::: 模板生成的应用中具有 [`@code`][1] 块的默认 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a :::no-loc(Blazor)::: template.</span></span> <span data-ttu-id="9e88c-174">HTML 标记、:::no-loc(Razor)::: 代码和 C# 代码位于同一个文件中：</span><span class="sxs-lookup"><span data-stu-id="9e88c-174">HTML markup, :::no-loc(Razor)::: code, and C# code are in the same file:</span></span>

<span data-ttu-id="9e88c-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-175">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="9e88c-176">还可以使用具有分部类的代码隐藏文件创建 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="9e88c-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="9e88c-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="9e88c-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-178">`Counter.razor.cs`:</span></span>

```csharp
namespace :::no-loc(Blazor):::Sample.Pages
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

<span data-ttu-id="9e88c-179">根据需要将任何所需的命名空间添加到分部类文件中。</span><span class="sxs-lookup"><span data-stu-id="9e88c-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="9e88c-180">:::no-loc(Razor)::: 组件使用的典型命名空间包括：</span><span class="sxs-lookup"><span data-stu-id="9e88c-180">Typical namespaces used by :::no-loc(Razor)::: components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="9e88c-181">`_Imports.razor` 文件中的 [`@using`][2] 指令仅适用于 :::no-loc(Razor)::: 文件 (`.razor`)，而不适用于 C# 文件 (`.cs`)。</span><span class="sxs-lookup"><span data-stu-id="9e88c-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to :::no-loc(Razor)::: files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="9e88c-182">指定基类</span><span class="sxs-lookup"><span data-stu-id="9e88c-182">Specify a base class</span></span>

<span data-ttu-id="9e88c-183">[`@inherits`][6] 指令可用于指定组件的基类。</span><span class="sxs-lookup"><span data-stu-id="9e88c-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="9e88c-184">下面的示例演示组件如何继承基类 `:::no-loc(Blazor):::RocksBase` 以提供组件的属性和方法。</span><span class="sxs-lookup"><span data-stu-id="9e88c-184">The following example shows how a component can inherit a base class, `:::no-loc(Blazor):::RocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="9e88c-185">基类应派生自 <xref:Microsoft.AspNetCore.Components.ComponentBase>。</span><span class="sxs-lookup"><span data-stu-id="9e88c-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="9e88c-186">`Pages/:::no-loc(Blazor):::Rocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-186">`Pages/:::no-loc(Blazor):::Rocks.razor`:</span></span>

```razor
@page "/:::no-loc(Blazor):::Rocks"
@inherits :::no-loc(Blazor):::RocksBase

<h1>@:::no-loc(Blazor):::RocksText</h1>
```

<span data-ttu-id="9e88c-187">`:::no-loc(Blazor):::RocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-187">`:::no-loc(Blazor):::RocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace :::no-loc(Blazor):::Sample
{
    public class :::no-loc(Blazor):::RocksBase : ComponentBase
    {
        public string :::no-loc(Blazor):::RocksText { get; set; } = 
            ":::no-loc(Blazor)::: rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="9e88c-188">使用组件</span><span class="sxs-lookup"><span data-stu-id="9e88c-188">Use components</span></span>

<span data-ttu-id="9e88c-189">通过使用 HTML 元素语法声明组件，组件可以包含其他组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="9e88c-190">使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。</span><span class="sxs-lookup"><span data-stu-id="9e88c-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="9e88c-191">`Pages/Index.razor` 中的以下标记呈现了 `HeadingComponent` 实例：</span><span class="sxs-lookup"><span data-stu-id="9e88c-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="9e88c-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="9e88c-193">如果某个组件包含一个 HTML 元素，该元素的大写首字母与组件名称不匹配，则会发出警告，指示该元素名称异常。</span><span class="sxs-lookup"><span data-stu-id="9e88c-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="9e88c-194">为组件的命名空间添加 [`@using`][2] 指令使组件可用，从而解决此警告。</span><span class="sxs-lookup"><span data-stu-id="9e88c-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="9e88c-195">参数</span><span class="sxs-lookup"><span data-stu-id="9e88c-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="9e88c-196">路由参数</span><span class="sxs-lookup"><span data-stu-id="9e88c-196">Route parameters</span></span>

<span data-ttu-id="9e88c-197">组件可以接收来自 [`@page`][9] 指令所提供的路由模板的路由参数。</span><span class="sxs-lookup"><span data-stu-id="9e88c-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="9e88c-198">路由器使用路由参数来填充相应的组件参数。</span><span class="sxs-lookup"><span data-stu-id="9e88c-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="9e88c-199">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-199">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="9e88c-200">不支持可选参数，因此在前面的示例中应用了两个 [`@page`][9] 指令。</span><span class="sxs-lookup"><span data-stu-id="9e88c-200">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="9e88c-201">第一个指令允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-201">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="9e88c-202">第二个 [`@page`][9] 指令会接收 `{text}` 路由参数，并将值赋予 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="9e88c-202">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="9e88c-203">要了解 catch-all 路由参数 `{*pageRoute}`（它可跨多个文件夹边界捕获路径），请参阅 <xref:blazor/fundamentals/routing#catch-all-route-parameters>。</span><span class="sxs-lookup"><span data-stu-id="9e88c-203">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="9e88c-204">组件参数</span><span class="sxs-lookup"><span data-stu-id="9e88c-204">Component parameters</span></span>

<span data-ttu-id="9e88c-205">组件可以有组件参数，这些参数是使用组件类中包含 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 特性的公共属性定义的。</span><span class="sxs-lookup"><span data-stu-id="9e88c-205">Components can have *component parameters* , which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="9e88c-206">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="9e88c-206">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="9e88c-207">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-207">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="9e88c-208">在示例应用的以下示例中，`ParentComponent` 设置 `ChildComponent` 的 `Title` 属性的值。</span><span class="sxs-lookup"><span data-stu-id="9e88c-208">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="9e88c-209">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-209">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="9e88c-210">请勿创建会写入其自己的组件参数的组件，而是使用私有字段。</span><span class="sxs-lookup"><span data-stu-id="9e88c-210">Don't create components that write to their own *component parameters* , use a private field instead.</span></span> <span data-ttu-id="9e88c-211">有关详细信息，请参阅[重写参数](#overwritten-parameters)部分。</span><span class="sxs-lookup"><span data-stu-id="9e88c-211">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="9e88c-212">子内容</span><span class="sxs-lookup"><span data-stu-id="9e88c-212">Child content</span></span>

<span data-ttu-id="9e88c-213">组件可以设置另一个组件的内容。</span><span class="sxs-lookup"><span data-stu-id="9e88c-213">Components can set the content of another component.</span></span> <span data-ttu-id="9e88c-214">分配组件提供指定接收组件的标记之间的内容。</span><span class="sxs-lookup"><span data-stu-id="9e88c-214">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="9e88c-215">在下面的示例中，`ChildComponent` 具有一个表示 <xref:Microsoft.AspNetCore.Components.RenderFragment>（表示要呈现的 UI 段）的 `ChildContent` 属性。</span><span class="sxs-lookup"><span data-stu-id="9e88c-215">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="9e88c-216">`ChildContent` 的值放置在应呈现内容的组件标记中。</span><span class="sxs-lookup"><span data-stu-id="9e88c-216">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="9e88c-217">`ChildContent` 的值是从父组件接收的，并呈现在启动面板的 `panel-body` 中。</span><span class="sxs-lookup"><span data-stu-id="9e88c-217">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="9e88c-218">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-218">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="9e88c-219">必须按约定将接收 <xref:Microsoft.AspNetCore.Components.RenderFragment> 内容的属性命名为 `ChildContent`。</span><span class="sxs-lookup"><span data-stu-id="9e88c-219">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="9e88c-220">示例应用中的 `ParentComponent` 可以通过将内容置于 `<ChildComponent>` 标记中，提供用于呈现 `ChildComponent` 的内容。</span><span class="sxs-lookup"><span data-stu-id="9e88c-220">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="9e88c-221">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-221">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="9e88c-222">由于 :::no-loc(Blazor)::: 呈现子内容的方式，如果在子组件的内容中使用递增循环变量，则在 `for` 循环内呈现组件需要本地索引变量：</span><span class="sxs-lookup"><span data-stu-id="9e88c-222">Due to the way that :::no-loc(Blazor)::: renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Param1="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="9e88c-223">或者，将 `foreach` 循环用于 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="9e88c-223">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Param1="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="9e88c-224">属性展开和任意参数</span><span class="sxs-lookup"><span data-stu-id="9e88c-224">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="9e88c-225">除了组件的声明参数外，组件还可以捕获和呈现其他属性。</span><span class="sxs-lookup"><span data-stu-id="9e88c-225">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="9e88c-226">其他属性可以在字典中捕获，然后在使用 [`@attributes`][3] :::no-loc(Razor)::: 指令呈现组件时，将其展开到元素上。</span><span class="sxs-lookup"><span data-stu-id="9e88c-226">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] :::no-loc(Razor)::: directive.</span></span> <span data-ttu-id="9e88c-227">此方案在定义生成支持各种自定义项的标记元素的组件时非常有用。</span><span class="sxs-lookup"><span data-stu-id="9e88c-227">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="9e88c-228">例如，为支持多个参数的 `<input>` 单独定义属性可能比较繁琐。</span><span class="sxs-lookup"><span data-stu-id="9e88c-228">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="9e88c-229">在下面的示例中，第一个 `<input>` 元素 (`id="useIndividualParams"`) 使用单个组件参数，第二个 `<input>` 元素 (`id="useAttributesDict"`) 使用属性展开：</span><span class="sxs-lookup"><span data-stu-id="9e88c-229">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

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

<span data-ttu-id="9e88c-230">参数的类型必须使用字符串键实现 `IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="9e88c-230">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="9e88c-231">使用这两种方法呈现的 `<input>` 元素相同：</span><span class="sxs-lookup"><span data-stu-id="9e88c-231">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="9e88c-232">若要接受任意特性，请使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 特性定义组件参数，并将 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="9e88c-232">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="9e88c-233">借助 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 上的 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 属性，参数可以匹配所有不匹配其他任何参数的特性。</span><span class="sxs-lookup"><span data-stu-id="9e88c-233">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="9e88c-234">组件只能使用 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 定义单个参数。</span><span class="sxs-lookup"><span data-stu-id="9e88c-234">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="9e88c-235">与 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 一起使用的属性类型必须可以使用字符串键从 `Dictionary<string, object>` 中分配。</span><span class="sxs-lookup"><span data-stu-id="9e88c-235">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="9e88c-236">`IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>` 也是此方案中的选项。</span><span class="sxs-lookup"><span data-stu-id="9e88c-236">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="9e88c-237">相对于元素特性位置的 [`@attributes`][3] 位置很重要。</span><span class="sxs-lookup"><span data-stu-id="9e88c-237">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="9e88c-238">在元素上展开 [`@attributes`][3] 时，将从右到左（从最后一个到第一个）处理特性。</span><span class="sxs-lookup"><span data-stu-id="9e88c-238">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="9e88c-239">请考虑以下使用 `Child` 组件的组件示例：</span><span class="sxs-lookup"><span data-stu-id="9e88c-239">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="9e88c-240">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-240">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="9e88c-241">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-241">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="9e88c-242">`Child` 组件的 `extra` 属性设置为 [`@attributes`][3] 右侧。</span><span class="sxs-lookup"><span data-stu-id="9e88c-242">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="9e88c-243">通过附加特性传递时，`Parent` 组件的呈现的 `<div>` 包含 `extra="5"`，因为特性是从右到左（从最后一个到第一个）处理的：</span><span class="sxs-lookup"><span data-stu-id="9e88c-243">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="9e88c-244">在下面的示例中，`extra` 和 [`@attributes`][3] 的顺序在 `Child` 组件的 `<div>` 中反转：</span><span class="sxs-lookup"><span data-stu-id="9e88c-244">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="9e88c-245">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-245">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="9e88c-246">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9e88c-246">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="9e88c-247">通过附加属性传递时，`Parent` 组件中呈现的 `<div>` 包含 `extra="10"`：</span><span class="sxs-lookup"><span data-stu-id="9e88c-247">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="9e88c-248">捕获对组件的引用</span><span class="sxs-lookup"><span data-stu-id="9e88c-248">Capture references to components</span></span>

<span data-ttu-id="9e88c-249">组件引用提供了一种引用组件实例的方法，以便可以向该实例发出命令，例如 `Show` 或 `Reset`。</span><span class="sxs-lookup"><span data-stu-id="9e88c-249">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="9e88c-250">若要捕获组件引用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9e88c-250">To capture a component reference:</span></span>

* <span data-ttu-id="9e88c-251">向子组件添加 [`@ref`][4] 特性。</span><span class="sxs-lookup"><span data-stu-id="9e88c-251">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="9e88c-252">定义与子组件类型相同的字段。</span><span class="sxs-lookup"><span data-stu-id="9e88c-252">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="9e88c-253">呈现组件时，将用 `MyLoginDialog` 子组件实例填充 `loginDialog` 字段。</span><span class="sxs-lookup"><span data-stu-id="9e88c-253">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="9e88c-254">然后，可以在组件实例上调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="9e88c-254">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9e88c-255">仅在呈现组件后填充 `loginDialog` 变量，其输出包含 `MyLoginDialog` 元素。</span><span class="sxs-lookup"><span data-stu-id="9e88c-255">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="9e88c-256">在呈现组件之前，没有任何可引用的内容。</span><span class="sxs-lookup"><span data-stu-id="9e88c-256">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="9e88c-257">若要在组件完成呈现后操作组件引用，请使用 [`OnAfterRenderAsync` 或 `OnAfterRender` 方法](xref:blazor/components/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="9e88c-257">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="9e88c-258">若要结合使用事件处理程序和引用变量，请使用 Lambda 表达式，或在 [`OnAfterRenderAsync` 或 `OnAfterRender` 方法](xref:blazor/components/lifecycle#after-component-render)中分配事件处理程序委托。</span><span class="sxs-lookup"><span data-stu-id="9e88c-258">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="9e88c-259">这可确保在分配事件处理程序之前先分配引用变量。</span><span class="sxs-lookup"><span data-stu-id="9e88c-259">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
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

<span data-ttu-id="9e88c-260">要引用循环中的组件，请参阅[捕获对多个相似子组件的引用 (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358)。</span><span class="sxs-lookup"><span data-stu-id="9e88c-260">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="9e88c-261">尽管捕获组件引用使用与[捕获元素引用](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)类似的语法，但它不是 JavaScript 互操作功能。</span><span class="sxs-lookup"><span data-stu-id="9e88c-261">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="9e88c-262">组件引用不会传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="9e88c-262">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="9e88c-263">组件引用只在 .NET 代码中使用。</span><span class="sxs-lookup"><span data-stu-id="9e88c-263">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="9e88c-264">不要使用组件引用来改变子组件的状态。</span><span class="sxs-lookup"><span data-stu-id="9e88c-264">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="9e88c-265">请改用常规声明性参数将数据传递给子组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-265">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="9e88c-266">使用常规声明性参数使子组件在正确的时间自动重新呈现。</span><span class="sxs-lookup"><span data-stu-id="9e88c-266">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="9e88c-267">同步上下文</span><span class="sxs-lookup"><span data-stu-id="9e88c-267">Synchronization context</span></span>

<span data-ttu-id="9e88c-268">:::no-loc(Blazor)::: 使用同步上下文 (<xref:System.Threading.SynchronizationContext>) 来强制执行单个逻辑线程。</span><span class="sxs-lookup"><span data-stu-id="9e88c-268">:::no-loc(Blazor)::: uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="9e88c-269">组件的[生命周期方法](xref:blazor/components/lifecycle)和 :::no-loc(Blazor)::: 引发的任何事件回调都在此同步上下文上执行。</span><span class="sxs-lookup"><span data-stu-id="9e88c-269">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by :::no-loc(Blazor)::: are executed on the synchronization context.</span></span>

<span data-ttu-id="9e88c-270">:::no-loc(Blazor Server):::的同步上下文尝试模拟单线程环境，使其与浏览器中的单线程 WebAssembly 模型紧密匹配。</span><span class="sxs-lookup"><span data-stu-id="9e88c-270">:::no-loc(Blazor Server):::'s synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="9e88c-271">在任意给定的时间点，工作只在一个线程上执行，从而造成单个逻辑线程的印象。</span><span class="sxs-lookup"><span data-stu-id="9e88c-271">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="9e88c-272">不会同时执行两个操作。</span><span class="sxs-lookup"><span data-stu-id="9e88c-272">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="9e88c-273">避免阻止线程的调用</span><span class="sxs-lookup"><span data-stu-id="9e88c-273">Avoid thread-blocking calls</span></span>

<span data-ttu-id="9e88c-274">通常，不要调用以下方法。</span><span class="sxs-lookup"><span data-stu-id="9e88c-274">Generally, don't call the following methods.</span></span> <span data-ttu-id="9e88c-275">以下方法阻止线程，进而阻止应用继续工作，直到基础 <xref:System.Threading.Tasks.Task> 完成：</span><span class="sxs-lookup"><span data-stu-id="9e88c-275">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="9e88c-276">在外部调用组件方法以更新状态</span><span class="sxs-lookup"><span data-stu-id="9e88c-276">Invoke component methods externally to update state</span></span>

<span data-ttu-id="9e88c-277">如果组件必须根据外部事件（如计时器或其他通知）进行更新，请使用 `InvokeAsync` 方法来调度到 :::no-loc(Blazor)::: 的同步上下文。</span><span class="sxs-lookup"><span data-stu-id="9e88c-277">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to :::no-loc(Blazor):::'s synchronization context.</span></span> <span data-ttu-id="9e88c-278">例如，假设有一个可通知任何侦听组件更新状态的通告程序服务：</span><span class="sxs-lookup"><span data-stu-id="9e88c-278">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="9e88c-279">注册 `NotifierService`：</span><span class="sxs-lookup"><span data-stu-id="9e88c-279">Register the `NotifierService`:</span></span>

* <span data-ttu-id="9e88c-280">在 :::no-loc(Blazor WebAssembly)::: 中，在 `Program.Main` 中将服务注册为单一实例：</span><span class="sxs-lookup"><span data-stu-id="9e88c-280">In :::no-loc(Blazor WebAssembly):::, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="9e88c-281">在 :::no-loc(Blazor Server)::: 中，在 `Startup.ConfigureServices` 中将服务注册为有作用域：</span><span class="sxs-lookup"><span data-stu-id="9e88c-281">In :::no-loc(Blazor Server):::, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="9e88c-282">使用 `NotifierService` 更新组件：</span><span class="sxs-lookup"><span data-stu-id="9e88c-282">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="9e88c-283">在前面的示例中，`NotifierService` 在 :::no-loc(Blazor)::: 的同步上下文之外调用组件的 `OnNotify` 方法。</span><span class="sxs-lookup"><span data-stu-id="9e88c-283">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of :::no-loc(Blazor):::'s synchronization context.</span></span> <span data-ttu-id="9e88c-284">`InvokeAsync` 用于切换到正确的上下文，并将呈现排入队列。</span><span class="sxs-lookup"><span data-stu-id="9e88c-284">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="9e88c-285">使用 \@ 键控制是否保留元素和组件</span><span class="sxs-lookup"><span data-stu-id="9e88c-285">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="9e88c-286">在呈现元素或组件的列表并且元素或组件随后发生更改时，:::no-loc(Blazor)::: 的比较算法必须决定之前的元素或组件中有哪些可以保留，以及模型对象应如何映射到这些元素或组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-286">When rendering a list of elements or components and the elements or components subsequently change, :::no-loc(Blazor):::'s diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="9e88c-287">通常，此过程是自动的，可以忽略，但在某些情况下，可能需要控制该过程。</span><span class="sxs-lookup"><span data-stu-id="9e88c-287">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="9e88c-288">请看下面的示例：</span><span class="sxs-lookup"><span data-stu-id="9e88c-288">Consider the following example:</span></span>

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

<span data-ttu-id="9e88c-289">`People` 集合的内容可能会随插入、删除或重新排序的条目而更改。</span><span class="sxs-lookup"><span data-stu-id="9e88c-289">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="9e88c-290">当组件重新呈现时，`<DetailsEditor>` 组件可能会更改以接收不同的 `Details` 参数值。</span><span class="sxs-lookup"><span data-stu-id="9e88c-290">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="9e88c-291">这可能导致重新呈现比预期更复杂。</span><span class="sxs-lookup"><span data-stu-id="9e88c-291">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="9e88c-292">在某些情况下，重新呈现可能会导致可见行为差异，如失去元素焦点。</span><span class="sxs-lookup"><span data-stu-id="9e88c-292">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="9e88c-293">可通过 [`@key`][5] 指令属性来控制映射过程。</span><span class="sxs-lookup"><span data-stu-id="9e88c-293">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="9e88c-294">[`@key`][5] 使比较算法保证基于键的值保留元素或组件：</span><span class="sxs-lookup"><span data-stu-id="9e88c-294">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="9e88c-295">当 `People` 集合发生更改时，比较算法会保留 `<DetailsEditor>` 实例与 `person` 实例之间的关联：</span><span class="sxs-lookup"><span data-stu-id="9e88c-295">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="9e88c-296">如果从 `People` 列表中删除 `Person`，则仅从 UI 中删除相应的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="9e88c-296">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="9e88c-297">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="9e88c-297">Other instances are left unchanged.</span></span>
* <span data-ttu-id="9e88c-298">如果在列表中的某个位置插入 `Person`，则会在相应位置插入一个新的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="9e88c-298">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="9e88c-299">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="9e88c-299">Other instances are left unchanged.</span></span>
* <span data-ttu-id="9e88c-300">如果对 `Person` 项进行重新排序，则保留相应的 `<DetailsEditor>` 实例，并在 UI 中重新排序。</span><span class="sxs-lookup"><span data-stu-id="9e88c-300">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="9e88c-301">在某些场景中，使用 [`@key`][5] 可最大程度降低重新呈现的复杂性，并避免在 DOM 的有状态部分发生变化时出现潜在问题，如焦点位置。</span><span class="sxs-lookup"><span data-stu-id="9e88c-301">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9e88c-302">对于每个容器元素或组件而言，键是本地的。</span><span class="sxs-lookup"><span data-stu-id="9e88c-302">Keys are local to each container element or component.</span></span> <span data-ttu-id="9e88c-303">不会在整个文档中全局地比较键。</span><span class="sxs-lookup"><span data-stu-id="9e88c-303">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="9e88c-304">何时使用 \@key</span><span class="sxs-lookup"><span data-stu-id="9e88c-304">When to use \@key</span></span>

<span data-ttu-id="9e88c-305">通常，每当列表呈现（例如，在 [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 块中）且存在合适的值来定义 [`@key`][5] 时，最好使用 [`@key`][5]。</span><span class="sxs-lookup"><span data-stu-id="9e88c-305">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="9e88c-306">还可以使用 [`@key`][5] 来防止 :::no-loc(Blazor)::: 在对象发生更改时保留元素或组件子树：</span><span class="sxs-lookup"><span data-stu-id="9e88c-306">You can also use [`@key`][5] to prevent :::no-loc(Blazor)::: from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="9e88c-307">如果 `@currentPerson` 更改，则 [`@key`][5] 属性指令强制 :::no-loc(Blazor)::: 放弃整个 `<div>` 及其子代，并利用新的元素和组件在 UI 中重新生成子树。</span><span class="sxs-lookup"><span data-stu-id="9e88c-307">If `@currentPerson` changes, the [`@key`][5] attribute directive forces :::no-loc(Blazor)::: to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="9e88c-308">如果需要确保在 `@currentPerson` 更改时不保留 UI 状态，这会很有用。</span><span class="sxs-lookup"><span data-stu-id="9e88c-308">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="9e88c-309">何时不使用 \@key</span><span class="sxs-lookup"><span data-stu-id="9e88c-309">When not to use \@key</span></span>

<span data-ttu-id="9e88c-310">与 [`@key`][5] 进行比较时，会产生性能成本。</span><span class="sxs-lookup"><span data-stu-id="9e88c-310">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="9e88c-311">性能成本不是很大，但仅在控制元素或组件保留规则对应用有利的情况下才指定 [`@key`][5]。</span><span class="sxs-lookup"><span data-stu-id="9e88c-311">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="9e88c-312">即使未使用 [`@key`][5]，:::no-loc(Blazor)::: 也会尽可能地保留子元素和组件实例。</span><span class="sxs-lookup"><span data-stu-id="9e88c-312">Even if [`@key`][5] isn't used, :::no-loc(Blazor)::: preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="9e88c-313">使用 [`@key`][5] 的唯一优点是控制如何将模型实例映射到保留的组件实例，而不是选择映射的比较算法。</span><span class="sxs-lookup"><span data-stu-id="9e88c-313">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="9e88c-314">\@key 使用哪些值</span><span class="sxs-lookup"><span data-stu-id="9e88c-314">What values to use for \@key</span></span>

<span data-ttu-id="9e88c-315">通常，为 [`@key`][5] 提供以下类型之一的值：</span><span class="sxs-lookup"><span data-stu-id="9e88c-315">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="9e88c-316">模型对象实例（例如，先前示例中的 `Person` 实例）。</span><span class="sxs-lookup"><span data-stu-id="9e88c-316">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="9e88c-317">这可确保基于对象引用相等性的保留。</span><span class="sxs-lookup"><span data-stu-id="9e88c-317">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="9e88c-318">唯一标识符（例如，`int`、`string` 或 `Guid` 类型的主键值）。</span><span class="sxs-lookup"><span data-stu-id="9e88c-318">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="9e88c-319">确保用于 [`@key`][5] 的值不冲突。</span><span class="sxs-lookup"><span data-stu-id="9e88c-319">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="9e88c-320">如果在同一父元素内检测到冲突值，则 :::no-loc(Blazor)::: 引发异常，因为它无法明确地将旧元素或组件映射到新元素或组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-320">If clashing values are detected within the same parent element, :::no-loc(Blazor)::: throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="9e88c-321">仅使用非重复值，例如对象实例或主键值。</span><span class="sxs-lookup"><span data-stu-id="9e88c-321">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="9e88c-322">重写参数</span><span class="sxs-lookup"><span data-stu-id="9e88c-322">Overwritten parameters</span></span>

<span data-ttu-id="9e88c-323">在父组件重新呈现时提供了新的参数值，这通常会重写现有参数。</span><span class="sxs-lookup"><span data-stu-id="9e88c-323">New parameter values are supplied, typically overwriting existing ones, when the parent component rerenders.</span></span>

<span data-ttu-id="9e88c-324">请考虑使用以下 `Expander` 组件，它们会：</span><span class="sxs-lookup"><span data-stu-id="9e88c-324">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="9e88c-325">呈现子内容。</span><span class="sxs-lookup"><span data-stu-id="9e88c-325">Renders child content.</span></span>
* <span data-ttu-id="9e88c-326">切换以使用组件参数显示子内容。</span><span class="sxs-lookup"><span data-stu-id="9e88c-326">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
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

<span data-ttu-id="9e88c-327">`Expander` 组件会添加到可调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>的父组件中：</span><span class="sxs-lookup"><span data-stu-id="9e88c-327">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

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

<span data-ttu-id="9e88c-328">最初，在切换 `Expanded` 属性时，`Expander` 组件独立地作出行为。</span><span class="sxs-lookup"><span data-stu-id="9e88c-328">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="9e88c-329">子组件会按预期方式维护其状态。</span><span class="sxs-lookup"><span data-stu-id="9e88c-329">The child components maintain their states as expected.</span></span> <span data-ttu-id="9e88c-330">在父组件中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 时，第一个子组件的 `Expanded` 会重新重置为它的初始值 (`true`)。</span><span class="sxs-lookup"><span data-stu-id="9e88c-330">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="9e88c-331">第二个 `Expander` 组件的 `Expanded` 值不会重置，因为第二个组件中没有呈现任何子内容。</span><span class="sxs-lookup"><span data-stu-id="9e88c-331">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="9e88c-332">要维持在前述情况中的状态，请在 `Expander` 组件中使用私有字段来保留它的切换状态。</span><span class="sxs-lookup"><span data-stu-id="9e88c-332">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="9e88c-333">以下经修定的 `Expander` 组件：</span><span class="sxs-lookup"><span data-stu-id="9e88c-333">The following revised `Expander` component:</span></span>

* <span data-ttu-id="9e88c-334">接受父项中的 `Expanded` 组件参数值。</span><span class="sxs-lookup"><span data-stu-id="9e88c-334">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="9e88c-335">将组件参数值分配给 [OnInitialized 事件](xref:blazor/components/lifecycle#component-initialization-methods)中的私有字段 (`expanded`)。</span><span class="sxs-lookup"><span data-stu-id="9e88c-335">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="9e88c-336">使用私有字段来维持它的内部切换状态。</span><span class="sxs-lookup"><span data-stu-id="9e88c-336">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
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

## <a name="apply-an-attribute"></a><span data-ttu-id="9e88c-337">应用属性</span><span class="sxs-lookup"><span data-stu-id="9e88c-337">Apply an attribute</span></span>

<span data-ttu-id="9e88c-338">可以通过 [`@attribute`][7] 指令在 :::no-loc(Razor)::: 组件中应用属性。</span><span class="sxs-lookup"><span data-stu-id="9e88c-338">Attributes can be applied to :::no-loc(Razor)::: components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="9e88c-339">下面的示例将 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性应用于组件类：</span><span class="sxs-lookup"><span data-stu-id="9e88c-339">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="9e88c-340">条件 HTML 元素属性</span><span class="sxs-lookup"><span data-stu-id="9e88c-340">Conditional HTML element attributes</span></span>

<span data-ttu-id="9e88c-341">HTML 元素属性基于 .NET 值有条件地呈现。</span><span class="sxs-lookup"><span data-stu-id="9e88c-341">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="9e88c-342">如果值为 `false` 或 `null`，则不会呈现属性。</span><span class="sxs-lookup"><span data-stu-id="9e88c-342">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="9e88c-343">如果值为 `true`，则以最小化呈现属性。</span><span class="sxs-lookup"><span data-stu-id="9e88c-343">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="9e88c-344">在下面的示例中，`IsCompleted` 确定是否在元素的标记中呈现 `checked`：</span><span class="sxs-lookup"><span data-stu-id="9e88c-344">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="9e88c-345">如果 `IsCompleted` 为 `true`，则复选框呈现为：</span><span class="sxs-lookup"><span data-stu-id="9e88c-345">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="9e88c-346">如果 `IsCompleted` 为 `false`，则复选框呈现为：</span><span class="sxs-lookup"><span data-stu-id="9e88c-346">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="9e88c-347">有关详细信息，请参阅 <xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="9e88c-347">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="9e88c-348">.NET 类型为 `bool` 时，某些 HTML 属性（如 [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)）无法正常运行。</span><span class="sxs-lookup"><span data-stu-id="9e88c-348">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="9e88c-349">在这些情况下，请使用 `string` 类型，而不是 `bool`。</span><span class="sxs-lookup"><span data-stu-id="9e88c-349">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="9e88c-350">原始 HTML</span><span class="sxs-lookup"><span data-stu-id="9e88c-350">Raw HTML</span></span>

<span data-ttu-id="9e88c-351">通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文字文本。</span><span class="sxs-lookup"><span data-stu-id="9e88c-351">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="9e88c-352">若要呈现原始 HTML，请将 HTML 内容包装在 `MarkupString` 值中。</span><span class="sxs-lookup"><span data-stu-id="9e88c-352">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="9e88c-353">将该值分析为 HTML 或 SVG，并插入到 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="9e88c-353">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="9e88c-354">呈现从任何不受信任的源构造的原始 HTML 存在安全风险，应避免！</span><span class="sxs-lookup"><span data-stu-id="9e88c-354">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="9e88c-355">下面的示例演示如何使用 `MarkupString` 类型向组件的呈现输出添加静态 HTML 内容块：</span><span class="sxs-lookup"><span data-stu-id="9e88c-355">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a><span data-ttu-id="9e88c-356">:::no-loc(Razor)::: 模板</span><span class="sxs-lookup"><span data-stu-id="9e88c-356">:::no-loc(Razor)::: templates</span></span>

<span data-ttu-id="9e88c-357">可以使用 :::no-loc(Razor)::: 模板语法来定义呈现片段。</span><span class="sxs-lookup"><span data-stu-id="9e88c-357">Render fragments can be defined using :::no-loc(Razor)::: template syntax.</span></span> <span data-ttu-id="9e88c-358">:::no-loc(Razor)::: 模板是一种定义 UI 代码片段的方法，请使用以下格式：</span><span class="sxs-lookup"><span data-stu-id="9e88c-358">:::no-loc(Razor)::: templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="9e88c-359">下面的示例演示如何在组件中指定 <xref:Microsoft.AspNetCore.Components.RenderFragment> 和 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 值并直接呈现模板。</span><span class="sxs-lookup"><span data-stu-id="9e88c-359">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="9e88c-360">还可以将呈现片段作为参数传递给[模板化组件](xref:blazor/components/templated-components)。</span><span class="sxs-lookup"><span data-stu-id="9e88c-360">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="9e88c-361">以上代码的呈现输出：</span><span class="sxs-lookup"><span data-stu-id="9e88c-361">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="9e88c-362">静态资产</span><span class="sxs-lookup"><span data-stu-id="9e88c-362">Static assets</span></span>

<span data-ttu-id="9e88c-363">:::no-loc(Blazor)::: 遵循 ASP.NET Core 应用的约定，将静态资产放在项目的 [`web root (wwwroot)` 文件夹](xref:fundamentals/index#web-root)下。</span><span class="sxs-lookup"><span data-stu-id="9e88c-363">:::no-loc(Blazor)::: follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="9e88c-364">使用基相对路径 (`/`) 来引用静态资产的 Web 根。</span><span class="sxs-lookup"><span data-stu-id="9e88c-364">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="9e88c-365">在下面的示例中，`logo.png` 实际位于 `{PROJECT ROOT}/wwwroot/images` 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="9e88c-365">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="9e88c-366">:::no-loc(Razor)::: 组件不支持波浪符斜杠表示法 (`~/`)。</span><span class="sxs-lookup"><span data-stu-id="9e88c-366">:::no-loc(Razor)::: components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="9e88c-367">有关设置应用基路径的信息，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="9e88c-367">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="9e88c-368">组件中不支持标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="9e88c-368">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="9e88c-369">:::no-loc(Razor)::: 组件（`.razor` 文件）不支持 [`Tag Helpers`](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="9e88c-369">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in :::no-loc(Razor)::: components (`.razor` files).</span></span> <span data-ttu-id="9e88c-370">若要在 :::no-loc(Blazor)::: 中提供类似标记帮助程序的功能，请创建一个具有与标记帮助程序相同功能的组件，并改为使用该组件。</span><span class="sxs-lookup"><span data-stu-id="9e88c-370">To provide Tag Helper-like functionality in :::no-loc(Blazor):::, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="9e88c-371">可缩放的向量图形 (SVG) 图像</span><span class="sxs-lookup"><span data-stu-id="9e88c-371">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="9e88c-372">由于 :::no-loc(Blazor)::: 呈现 HTML，因此通过 `<img>` 标记支持浏览器支持的图像，包括可缩放的矢量图形 (SVG) 图像(`.svg`)：</span><span class="sxs-lookup"><span data-stu-id="9e88c-372">Since :::no-loc(Blazor)::: renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="9e88c-373">同样，样式表文件 (`.css`) 的 CSS 规则支持 SVG 图像：</span><span class="sxs-lookup"><span data-stu-id="9e88c-373">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="9e88c-374">但是，并非在所有情况下都支持内联的 SVG 标记。</span><span class="sxs-lookup"><span data-stu-id="9e88c-374">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="9e88c-375">如果将 `<svg>` 标记直接放入组件文件 (`.razor`)，则支持基本图像呈现，但很多高级场景尚不受支持。</span><span class="sxs-lookup"><span data-stu-id="9e88c-375">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="9e88c-376">例如，当前未遵循 `<use>` 标记，并且 [`@bind`][10] 不能与某些 SVG 标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="9e88c-376">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="9e88c-377">有关详细信息，请参阅 [:::no-loc(Blazor)::: (dotnet/aspnetcore #18271) 中的 SVG 支持](https://github.com/dotnet/aspnetcore/issues/18271)。</span><span class="sxs-lookup"><span data-stu-id="9e88c-377">For more information, see [SVG support in :::no-loc(Blazor)::: (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e88c-378">其他资源</span><span class="sxs-lookup"><span data-stu-id="9e88c-378">Additional resources</span></span>

* <span data-ttu-id="9e88c-379"><xref:blazor/security/server/threat-mitigation>：包括有关如何生成必须应对资源耗尽的 :::no-loc(Blazor Server):::应用的指南。</span><span class="sxs-lookup"><span data-stu-id="9e88c-379"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building :::no-loc(Blazor Server)::: apps that must contend with resource exhaustion.</span></span>

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
