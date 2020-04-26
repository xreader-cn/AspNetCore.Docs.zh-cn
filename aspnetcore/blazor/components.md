---
title: 创建和使用 ASP.NET Core Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件，包括如何绑定到数据、处理事件和管理组件生命周期。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 4434636992cb2506ef6525996690946f97c43764
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791490"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="88ced-103">创建和使用 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="88ced-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="88ced-104">作者：[Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27) 和 [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="88ced-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="88ced-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="88ced-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="88ced-106"> 应用是使用组件构建的  。</span><span class="sxs-lookup"><span data-stu-id="88ced-106"> apps are built using *components*.</span></span> <span data-ttu-id="88ced-107">组件是自包含的用户界面 (UI) 块，例如页、对话框或窗体。</span><span class="sxs-lookup"><span data-stu-id="88ced-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="88ced-108">组件包含插入数据或响应 UI 事件所需的 HTML 标记和处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="88ced-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="88ced-109">组件非常灵活且轻量。</span><span class="sxs-lookup"><span data-stu-id="88ced-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="88ced-110">可在项目之间嵌套、重复使用和共享。</span><span class="sxs-lookup"><span data-stu-id="88ced-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="88ced-111">组件类</span><span class="sxs-lookup"><span data-stu-id="88ced-111">Component classes</span></span>

<span data-ttu-id="88ced-112">组件是使用 C# 和 HTML 标记的组合在 [Razor](xref:mvc/views/razor) 组件文件 (.razor) 中实现的  。</span><span class="sxs-lookup"><span data-stu-id="88ced-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="88ced-113">Blazor 中的组件正式称为 Razor 组件  。</span><span class="sxs-lookup"><span data-stu-id="88ced-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="88ced-114">组件的名称必须以大写字符开头。</span><span class="sxs-lookup"><span data-stu-id="88ced-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="88ced-115">例如，MyCoolComponent.razor 有效，myCoolComponent.razor 无效   。</span><span class="sxs-lookup"><span data-stu-id="88ced-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="88ced-116">使用 HTML 定义组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="88ced-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="88ced-117">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="88ced-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="88ced-118">在编译应用时，HTML 标记和 C# 呈现逻辑转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="88ced-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="88ced-119">生成的类的名称与文件名匹配。</span><span class="sxs-lookup"><span data-stu-id="88ced-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="88ced-120">组件类的成员在 `@code` 块中定义。</span><span class="sxs-lookup"><span data-stu-id="88ced-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="88ced-121">在 `@code` 块中，通过用于处理事件或定义其他组件逻辑的方法指定组件状态（属性、字段）。</span><span class="sxs-lookup"><span data-stu-id="88ced-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="88ced-122">允许多个 `@code` 块。</span><span class="sxs-lookup"><span data-stu-id="88ced-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="88ced-123">可以使用以 `@` 开头的 C# 表达式将组件成员作为组件的呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="88ced-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="88ced-124">例如，通过为字段名称添加 `@` 前缀来呈现 C# 字段。</span><span class="sxs-lookup"><span data-stu-id="88ced-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="88ced-125">下面的示例计算并呈现：</span><span class="sxs-lookup"><span data-stu-id="88ced-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="88ced-126">`font-style` 的 CSS 属性值的 `_headingFontStyle`。</span><span class="sxs-lookup"><span data-stu-id="88ced-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="88ced-127">`<h1>` 元素内容的 `_headingText`。</span><span class="sxs-lookup"><span data-stu-id="88ced-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="88ced-128">最初呈现组件后，组件会为响应事件而重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="88ced-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="88ced-129">然后 Blazor 将新呈现树与前一个呈现树进行比较，并对浏览器文档对象模型 (DOM) 应用任何修改。</span><span class="sxs-lookup"><span data-stu-id="88ced-129">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="88ced-130">组件是普通 C# 类，可以放置在项目中的任何位置。</span><span class="sxs-lookup"><span data-stu-id="88ced-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="88ced-131">生成网页的组件通常位于 Pages 文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="88ced-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="88ced-132">非页面组件通常放置在 Shared 文件夹或添加到项目的自定义文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="88ced-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="88ced-133">通常，组件的命名空间是从应用的根命名空间和该组件在应用内的位置（文件夹）派生而来的。</span><span class="sxs-lookup"><span data-stu-id="88ced-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="88ced-134">如果应用的根命名空间是 `BlazorApp`，并且 `Counter` 组件位于 Pages 文件夹中  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="88ced-135">`Counter` 组件的命名空间为 `BlazorApp.Pages`。</span><span class="sxs-lookup"><span data-stu-id="88ced-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="88ced-136">组件的完全限定类型名称为 `BlazorApp.Pages.Counter`。</span><span class="sxs-lookup"><span data-stu-id="88ced-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="88ced-137">有关详细信息，请参阅[导入组件](#import-components)部分。</span><span class="sxs-lookup"><span data-stu-id="88ced-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="88ced-138">若要使用自定义文件夹，请将自定义文件夹的命名空间添加到父组件或应用的 _Imports.razor 文件中  。</span><span class="sxs-lookup"><span data-stu-id="88ced-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="88ced-139">例如，当应用的根命名空间为 `BlazorApp` 时，以下命名空间使 Components 文件夹中的组件可用  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="88ced-140">静态资产</span><span class="sxs-lookup"><span data-stu-id="88ced-140">Static assets</span></span>

Blazor<span data-ttu-id="88ced-141"> 遵循 ASP.NET Core 应用的约定，将静态资产放在项目的 [Web 根 (wwwroot) 文件夹下](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="88ced-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="88ced-142">使用基相对路径 (`/`) 来引用静态资产的 Web 根。</span><span class="sxs-lookup"><span data-stu-id="88ced-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="88ced-143">在下面的示例中，logo.png 物理上位置位于 {PROJECT ROOT}/wwwroot/images 文件夹中   ：</span><span class="sxs-lookup"><span data-stu-id="88ced-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="88ced-144">Razor 组件不支持波浪符斜杠表示法 (`~/`)  。</span><span class="sxs-lookup"><span data-stu-id="88ced-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="88ced-145">有关设置应用基路径的信息，请参阅 <xref:host-and-deploy/blazor/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="88ced-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="88ced-146">组件中不支持标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="88ced-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="88ced-147">Razor 组件（.razor 文件）不支持[标记帮助程序](xref:mvc/views/tag-helpers/intro)  。</span><span class="sxs-lookup"><span data-stu-id="88ced-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="88ced-148">若要在 Blazor 中提供类似标记帮助程序的功能，请创建一个具有与标记帮助程序相同功能的组件，并改为使用该组件。</span><span class="sxs-lookup"><span data-stu-id="88ced-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="88ced-149">使用组件</span><span class="sxs-lookup"><span data-stu-id="88ced-149">Use components</span></span>

<span data-ttu-id="88ced-150">通过使用 HTML 元素语法声明组件，组件可以包含其他组件。</span><span class="sxs-lookup"><span data-stu-id="88ced-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="88ced-151">使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。</span><span class="sxs-lookup"><span data-stu-id="88ced-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="88ced-152">Index.razor 中的以下标记呈现了 `HeadingComponent` 实例  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="88ced-153">Components/HeadingComponent.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="88ced-154">如果某个组件包含一个 HTML 元素，该元素的大写首字母与组件名称不匹配，则会发出警告，指示该元素名称异常。</span><span class="sxs-lookup"><span data-stu-id="88ced-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="88ced-155">为组件的命名空间添加 `@using` 指令使组件可用，这可解决此警告。</span><span class="sxs-lookup"><span data-stu-id="88ced-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="88ced-156">路由</span><span class="sxs-lookup"><span data-stu-id="88ced-156">Routing</span></span>

<span data-ttu-id="88ced-157">可以通过为应用中的每个可访问组件提供路由模板来实现 Blazor 中的路由。</span><span class="sxs-lookup"><span data-stu-id="88ced-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="88ced-158">编译具有 `@page` 指令的 Razor 文件时，将为生成的类提供 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 指定路由模板。</span><span class="sxs-lookup"><span data-stu-id="88ced-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="88ced-159">在运行时，路由器将使用 `RouteAttribute` 查找组件类，并呈现具有与请求的 URL 匹配的路由模板的任何组件。</span><span class="sxs-lookup"><span data-stu-id="88ced-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="88ced-160">有关详细信息，请参阅 <xref:blazor/routing>。</span><span class="sxs-lookup"><span data-stu-id="88ced-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="88ced-161">参数</span><span class="sxs-lookup"><span data-stu-id="88ced-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="88ced-162">路由参数</span><span class="sxs-lookup"><span data-stu-id="88ced-162">Route parameters</span></span>

<span data-ttu-id="88ced-163">组件可以接收 `@page` 指令中提供的路由模板中的路由参数。</span><span class="sxs-lookup"><span data-stu-id="88ced-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="88ced-164">路由器使用路由参数来填充相应的组件参数。</span><span class="sxs-lookup"><span data-stu-id="88ced-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="88ced-165">Pages/RouteParameter.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="88ced-166">不支持可选参数，因此在前面的示例中应用了两个 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="88ced-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="88ced-167">第一个指令允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="88ced-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="88ced-168">第二个 `@page` 指令接收 `{text}` 路由参数，并将值分配给 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="88ced-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="88ced-169">Razor 组件 (.razor) 不支持 Catch-all 参数语法 (`*`/`**`)，该语法捕获跨多个文件夹边界的路径    。</span><span class="sxs-lookup"><span data-stu-id="88ced-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="88ced-170">组件参数</span><span class="sxs-lookup"><span data-stu-id="88ced-170">Component parameters</span></span>

<span data-ttu-id="88ced-171">组件可以具有组件参数，这些参数由具有 `[Parameter]` 属性的组件类上的公共属性定义  。</span><span class="sxs-lookup"><span data-stu-id="88ced-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="88ced-172">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="88ced-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="88ced-173">Components/ChildComponent.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="88ced-174">在示例应用的以下示例中，`ParentComponent` 设置 `ChildComponent` 的 `Title` 属性的值。</span><span class="sxs-lookup"><span data-stu-id="88ced-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="88ced-175">Pages/ParentComponent  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="88ced-176">请勿创建会写入其自己的组件参数的组件，而是使用私有字段  。</span><span class="sxs-lookup"><span data-stu-id="88ced-176">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="88ced-177">有关详细信息，请参阅[请勿创建会写入其自己的组参数属性的组件](#dont-create-components-that-write-to-their-own-parameter-properties)部分。</span><span class="sxs-lookup"><span data-stu-id="88ced-177">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="88ced-178">子内容</span><span class="sxs-lookup"><span data-stu-id="88ced-178">Child content</span></span>

<span data-ttu-id="88ced-179">组件可以设置另一个组件的内容。</span><span class="sxs-lookup"><span data-stu-id="88ced-179">Components can set the content of another component.</span></span> <span data-ttu-id="88ced-180">分配组件提供指定接收组件的标记之间的内容。</span><span class="sxs-lookup"><span data-stu-id="88ced-180">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="88ced-181">在下面的示例中，`ChildComponent` 具有一个表示 `RenderFragment`（表示要呈现的 UI 段）的 `ChildContent` 属性。</span><span class="sxs-lookup"><span data-stu-id="88ced-181">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="88ced-182">`ChildContent` 的值放置在应呈现内容的组件标记中。</span><span class="sxs-lookup"><span data-stu-id="88ced-182">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="88ced-183">`ChildContent` 的值是从父组件接收的，并呈现在启动面板的 `panel-body` 中。</span><span class="sxs-lookup"><span data-stu-id="88ced-183">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="88ced-184">Components/ChildComponent.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-184">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="88ced-185">必须按约定将接收 `RenderFragment` 内容的属性命名为 `ChildContent`。</span><span class="sxs-lookup"><span data-stu-id="88ced-185">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="88ced-186">示例应用中的 `ParentComponent` 可以通过将内容置于 `<ChildComponent>` 标记中，提供用于呈现 `ChildComponent` 的内容。</span><span class="sxs-lookup"><span data-stu-id="88ced-186">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="88ced-187">Pages/ParentComponent.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="88ced-188">属性展开和任意参数</span><span class="sxs-lookup"><span data-stu-id="88ced-188">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="88ced-189">除了组件的声明参数外，组件还可以捕获和呈现其他属性。</span><span class="sxs-lookup"><span data-stu-id="88ced-189">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="88ced-190">其他属性可以在字典中捕获，然后在使用 [`@attributes`](xref:mvc/views/razor#attributes) Razor 指令呈现组件时，将其展开到元素上  。</span><span class="sxs-lookup"><span data-stu-id="88ced-190">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="88ced-191">此方案在定义生成支持各种自定义项的标记元素的组件时非常有用。</span><span class="sxs-lookup"><span data-stu-id="88ced-191">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="88ced-192">例如，为支持多个参数的 `<input>` 单独定义属性可能比较繁琐。</span><span class="sxs-lookup"><span data-stu-id="88ced-192">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="88ced-193">在下面的示例中，第一个 `<input>` 元素 (`id="useIndividualParams"`) 使用单个组件参数，第二个 `<input>` 元素 (`id="useAttributesDict"`) 使用属性展开：</span><span class="sxs-lookup"><span data-stu-id="88ced-193">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="88ced-194">参数的类型必须使用字符串键实现 `IEnumerable<KeyValuePair<string, object>>`。</span><span class="sxs-lookup"><span data-stu-id="88ced-194">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="88ced-195">在此方案中，也可以选择使用 `IReadOnlyDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="88ced-195">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="88ced-196">使用这两种方法呈现的 `<input>` 元素相同：</span><span class="sxs-lookup"><span data-stu-id="88ced-196">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="88ced-197">若要接受任意属性，请使用 `[Parameter]` 特性定义组件参数，并将 `CaptureUnmatchedValues` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="88ced-197">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="88ced-198">`[Parameter]` 上的 `CaptureUnmatchedValues` 属性允许参数匹配所有不匹配任何其他参数的特性。</span><span class="sxs-lookup"><span data-stu-id="88ced-198">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="88ced-199">组件只能使用 `CaptureUnmatchedValues` 定义单个参数。</span><span class="sxs-lookup"><span data-stu-id="88ced-199">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="88ced-200">与 `CaptureUnmatchedValues` 一起使用的属性类型必须可以使用字符串键从 `Dictionary<string, object>` 中分配。</span><span class="sxs-lookup"><span data-stu-id="88ced-200">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="88ced-201">`IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>` 也是此方案中的选项。</span><span class="sxs-lookup"><span data-stu-id="88ced-201">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="88ced-202">相对于元素特性位置的 `@attributes` 位置很重要。</span><span class="sxs-lookup"><span data-stu-id="88ced-202">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="88ced-203">在元素上展开 `@attributes` 时，将从右到左（从最后一个到第一个）处理特性。</span><span class="sxs-lookup"><span data-stu-id="88ced-203">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="88ced-204">请考虑以下使用 `Child` 组件的组件示例：</span><span class="sxs-lookup"><span data-stu-id="88ced-204">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="88ced-205">ParentComponent.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-205">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="88ced-206">ChildComponent.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-206">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="88ced-207">`Child` 组件的 `extra` 属性设置为 `@attributes` 右侧。</span><span class="sxs-lookup"><span data-stu-id="88ced-207">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="88ced-208">通过附加特性传递时，`Parent` 组件的呈现的 `<div>` 包含 `extra="5"`，因为特性是从右到左（从最后一个到第一个）处理的：</span><span class="sxs-lookup"><span data-stu-id="88ced-208">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="88ced-209">在下面的示例中，`extra` 和 `@attributes` 的顺序在 `Child` 组件的 `<div>` 中反转：</span><span class="sxs-lookup"><span data-stu-id="88ced-209">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="88ced-210">ParentComponent.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-210">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="88ced-211">ChildComponent.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-211">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="88ced-212">通过附加属性传递时，`Parent` 组件中呈现的 `<div>` 包含 `extra="10"`：</span><span class="sxs-lookup"><span data-stu-id="88ced-212">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="88ced-213">捕获对组件的引用</span><span class="sxs-lookup"><span data-stu-id="88ced-213">Capture references to components</span></span>

<span data-ttu-id="88ced-214">组件引用提供了一种引用组件实例的方法，以便可以向该实例发出命令，例如 `Show` 或 `Reset`。</span><span class="sxs-lookup"><span data-stu-id="88ced-214">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="88ced-215">若要捕获组件引用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="88ced-215">To capture a component reference:</span></span>

* <span data-ttu-id="88ced-216">向子组件添加 [`@ref`](xref:mvc/views/razor#ref) 特性。</span><span class="sxs-lookup"><span data-stu-id="88ced-216">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="88ced-217">定义与子组件类型相同的字段。</span><span class="sxs-lookup"><span data-stu-id="88ced-217">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="88ced-218">呈现组件时，将用 `MyLoginDialog` 子组件实例填充 `_loginDialog` 字段。</span><span class="sxs-lookup"><span data-stu-id="88ced-218">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="88ced-219">然后，可以在组件实例上调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="88ced-219">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="88ced-220">仅在呈现组件后填充 `_loginDialog` 变量，其输出包含 `MyLoginDialog` 元素。</span><span class="sxs-lookup"><span data-stu-id="88ced-220">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="88ced-221">在这之前，没有任何内容可引用。</span><span class="sxs-lookup"><span data-stu-id="88ced-221">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="88ced-222">若要在组件完成呈现后操作组件引用，请使用 [OnAfterRenderAsync 或 OnAfterRender 方法](xref:blazor/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="88ced-222">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="88ced-223">要引用循环中的组件，请参阅[捕获对多个相似子组件的引用 (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358)。</span><span class="sxs-lookup"><span data-stu-id="88ced-223">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="88ced-224">尽管捕获组件引用使用与[捕获元素引用](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)类似的语法，但它不是 JavaScript 互操作功能。</span><span class="sxs-lookup"><span data-stu-id="88ced-224">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="88ced-225">组件引用不会传递给 JavaScript 代码&mdash;它们只在 .NET 代码中使用。</span><span class="sxs-lookup"><span data-stu-id="88ced-225">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="88ced-226">不要使用组件引用来改变子组件的状态  。</span><span class="sxs-lookup"><span data-stu-id="88ced-226">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="88ced-227">请改用常规声明性参数将数据传递给子组件。</span><span class="sxs-lookup"><span data-stu-id="88ced-227">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="88ced-228">使用常规声明性参数使子组件在正确的时间自动重新呈现。</span><span class="sxs-lookup"><span data-stu-id="88ced-228">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="88ced-229">在外部调用组件方法以更新状态</span><span class="sxs-lookup"><span data-stu-id="88ced-229">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="88ced-230"> 使用 `SynchronizationContext` 来强制执行单个逻辑线程。</span><span class="sxs-lookup"><span data-stu-id="88ced-230"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="88ced-231">组件的[生命周期方法](xref:blazor/lifecycle)和 Blazor 引发的任何事件回调都在此 `SynchronizationContext` 上执行。</span><span class="sxs-lookup"><span data-stu-id="88ced-231">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="88ced-232">如果组件必须根据外部事件（如计时器或其他通知）进行更新，请使用 `InvokeAsync` 方法，该方法将调度到 Blazor 的 `SynchronizationContext`。</span><span class="sxs-lookup"><span data-stu-id="88ced-232">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="88ced-233">例如，假设有一个可通知任何侦听组件更新状态的通告程序服务  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-233">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="88ced-234">将 `NotifierService` 注册为单一实例：</span><span class="sxs-lookup"><span data-stu-id="88ced-234">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="88ced-235">在 Blazor WebAssembly 中，在 `Program.Main` 中注册服务：</span><span class="sxs-lookup"><span data-stu-id="88ced-235">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="88ced-236">在 Blazor 服务器中，在 `Startup.ConfigureServices` 中注册服务：</span><span class="sxs-lookup"><span data-stu-id="88ced-236">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="88ced-237">使用 `NotifierService` 更新组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-237">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="88ced-238">在前面的示例中，`NotifierService` 在 Blazor 的 `SynchronizationContext` 之外调用组件的 `OnNotify` 方法。</span><span class="sxs-lookup"><span data-stu-id="88ced-238">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="88ced-239">`InvokeAsync` 用于切换到正确的上下文，并将呈现排入队列。</span><span class="sxs-lookup"><span data-stu-id="88ced-239">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="88ced-240">使用 \@ 键控制是否保留元素和组件</span><span class="sxs-lookup"><span data-stu-id="88ced-240">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="88ced-241">在呈现元素或组件的列表并且元素或组件随后发生更改时，Blazor 的比较算法必须决定之前的元素或组件中有哪些可以保留，以及模型对象应如何映射到这些元素或组件。</span><span class="sxs-lookup"><span data-stu-id="88ced-241">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="88ced-242">通常，此过程是自动的，可以忽略，但在某些情况下，可能需要控制该过程。</span><span class="sxs-lookup"><span data-stu-id="88ced-242">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="88ced-243">请看下面的示例：</span><span class="sxs-lookup"><span data-stu-id="88ced-243">Consider the following example:</span></span>

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

<span data-ttu-id="88ced-244">`People` 集合的内容可能会随插入、删除或重新排序的条目而更改。</span><span class="sxs-lookup"><span data-stu-id="88ced-244">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="88ced-245">当组件重新呈现时，`<DetailsEditor>` 组件可能会更改以接收不同的 `Details` 参数值。</span><span class="sxs-lookup"><span data-stu-id="88ced-245">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="88ced-246">这可能导致重新呈现比预期更复杂。</span><span class="sxs-lookup"><span data-stu-id="88ced-246">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="88ced-247">在某些情况下，重新呈现可能会导致可见行为差异，如失去元素焦点。</span><span class="sxs-lookup"><span data-stu-id="88ced-247">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="88ced-248">可通过 [`@key`](xref:mvc/views/razor#key) 指令属性来控制映射过程。</span><span class="sxs-lookup"><span data-stu-id="88ced-248">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="88ced-249">`@key` 使比较算法保证基于键的值保留元素或组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-249">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="88ced-250">当 `People` 集合发生更改时，比较算法会保留 `<DetailsEditor>` 实例与 `person` 实例之间的关联：</span><span class="sxs-lookup"><span data-stu-id="88ced-250">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="88ced-251">如果从 `People` 列表中删除 `Person`，则仅从 UI 中删除相应的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="88ced-251">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="88ced-252">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="88ced-252">Other instances are left unchanged.</span></span>
* <span data-ttu-id="88ced-253">如果在列表中的某个位置插入 `Person`，则会在相应位置插入一个新的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="88ced-253">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="88ced-254">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="88ced-254">Other instances are left unchanged.</span></span>
* <span data-ttu-id="88ced-255">如果对 `Person` 项进行重新排序，则保留相应的 `<DetailsEditor>` 实例，并在 UI 中重新排序。</span><span class="sxs-lookup"><span data-stu-id="88ced-255">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="88ced-256">在某些场景中，使用 `@key` 可最大程度降低重新呈现的复杂性，并避免在 DOM 的有状态部分发生变化时出现潜在问题，如焦点位置。</span><span class="sxs-lookup"><span data-stu-id="88ced-256">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="88ced-257">对于每个容器元素或组件而言，键是本地的。</span><span class="sxs-lookup"><span data-stu-id="88ced-257">Keys are local to each container element or component.</span></span> <span data-ttu-id="88ced-258">不会在整个文档中全局地比较键。</span><span class="sxs-lookup"><span data-stu-id="88ced-258">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="88ced-259">何时使用 \@key</span><span class="sxs-lookup"><span data-stu-id="88ced-259">When to use \@key</span></span>

<span data-ttu-id="88ced-260">通常，每当呈现列表（例如，在 `@foreach` 块中）以及存在适当的值定义 `@key` 时，都可以使用 `@key`。</span><span class="sxs-lookup"><span data-stu-id="88ced-260">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="88ced-261">还可以使用 `@key` 来防止 Blazor 在对象发生更改时保留元素或组件子树：</span><span class="sxs-lookup"><span data-stu-id="88ced-261">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="88ced-262">如果 `@currentPerson` 更改，则 `@key` 属性指令强制 Blazor 放弃整个 `<div>` 及其子代，并利用新的元素和组件在 UI 中重新生成子树。</span><span class="sxs-lookup"><span data-stu-id="88ced-262">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="88ced-263">如果需要确保在 `@currentPerson` 更改时不保留 UI 状态，这会很有用。</span><span class="sxs-lookup"><span data-stu-id="88ced-263">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="88ced-264">何时不使用 \@key</span><span class="sxs-lookup"><span data-stu-id="88ced-264">When not to use \@key</span></span>

<span data-ttu-id="88ced-265">与 `@key` 进行比较时，会产生性能费用。</span><span class="sxs-lookup"><span data-stu-id="88ced-265">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="88ced-266">性能费用不是很大，但仅在控制元素或组件保留规则对应用有利的情况下才指定 `@key`。</span><span class="sxs-lookup"><span data-stu-id="88ced-266">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="88ced-267">即使未使用 `@key`，Blazor 也会尽可能地保留子元素和组件实例。</span><span class="sxs-lookup"><span data-stu-id="88ced-267">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="88ced-268">使用 `@key` 的唯一优点是控制如何将模型实例映射到保留的组件实例，而不是选择映射的比较算法  。</span><span class="sxs-lookup"><span data-stu-id="88ced-268">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="88ced-269">\@key 使用哪些值</span><span class="sxs-lookup"><span data-stu-id="88ced-269">What values to use for \@key</span></span>

<span data-ttu-id="88ced-270">通常，为 `@key` 提供以下类型之一的值：</span><span class="sxs-lookup"><span data-stu-id="88ced-270">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="88ced-271">模型对象实例（例如，先前示例中的 `Person` 实例）。</span><span class="sxs-lookup"><span data-stu-id="88ced-271">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="88ced-272">这可确保基于对象引用相等性的保留。</span><span class="sxs-lookup"><span data-stu-id="88ced-272">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="88ced-273">唯一标识符（例如，`int`、`string` 或 `Guid` 类型的主键值）。</span><span class="sxs-lookup"><span data-stu-id="88ced-273">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="88ced-274">确保用于 `@key` 的值不冲突。</span><span class="sxs-lookup"><span data-stu-id="88ced-274">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="88ced-275">如果在同一父元素内检测到冲突值，则 Blazor 引发异常，因为它无法明确地将旧元素或组件映射到新元素或组件。</span><span class="sxs-lookup"><span data-stu-id="88ced-275">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="88ced-276">仅使用非重复值，例如对象实例或主键值。</span><span class="sxs-lookup"><span data-stu-id="88ced-276">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="88ced-277">请勿创建会写入其自己的组参数属性的组件</span><span class="sxs-lookup"><span data-stu-id="88ced-277">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="88ced-278">在以下情况中，会重写参数：</span><span class="sxs-lookup"><span data-stu-id="88ced-278">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="88ced-279">子组件的内容使用 `RenderFragment` 进行呈现。</span><span class="sxs-lookup"><span data-stu-id="88ced-279">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="88ced-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 在父组件中调用。</span><span class="sxs-lookup"><span data-stu-id="88ced-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="88ced-281">由于在调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 且向子组件提供新的参数值时，会重新呈现父组件，因此将重置参数。</span><span class="sxs-lookup"><span data-stu-id="88ced-281">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="88ced-282">请考虑使用以下 `Expander` 组件，它们会：</span><span class="sxs-lookup"><span data-stu-id="88ced-282">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="88ced-283">呈现子内容。</span><span class="sxs-lookup"><span data-stu-id="88ced-283">Renders child content.</span></span>
* <span data-ttu-id="88ced-284">切换以使用组件参数显示子内容。</span><span class="sxs-lookup"><span data-stu-id="88ced-284">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
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

<span data-ttu-id="88ced-285">`Expander` 组件会添加到可调用 `StateHasChanged`的父组件中：</span><span class="sxs-lookup"><span data-stu-id="88ced-285">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="88ced-286">最初，在切换 `Expanded` 属性时，`Expander` 组件独立地作出行为。</span><span class="sxs-lookup"><span data-stu-id="88ced-286">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="88ced-287">子组件会按预期方式维护其状态。</span><span class="sxs-lookup"><span data-stu-id="88ced-287">The child components maintain their states as expected.</span></span> <span data-ttu-id="88ced-288">在父组件中调用 `StateHasChanged` 时，第一个子组件的 `Expanded` 会重新重置为它的初始值 (`true`)。</span><span class="sxs-lookup"><span data-stu-id="88ced-288">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="88ced-289">第二个 `Expander` 组件的 `Expanded` 值不会重置，因为第二个组件中没有呈现任何子内容。</span><span class="sxs-lookup"><span data-stu-id="88ced-289">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="88ced-290">要维持在前述情况中的状态，请在 `Expander` 组件中使用私有字段来保留它的切换状态  。</span><span class="sxs-lookup"><span data-stu-id="88ced-290">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="88ced-291">以下 `Expander` 组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-291">The following `Expander` component:</span></span>

* <span data-ttu-id="88ced-292">接受父项中的 `Expanded` 组件参数值。</span><span class="sxs-lookup"><span data-stu-id="88ced-292">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="88ced-293">将组件参数值分配给 [OnInitialized 事件](xref:blazor/lifecycle#component-initialization-methods)中的私有字段 (`_expanded`)  。</span><span class="sxs-lookup"><span data-stu-id="88ced-293">Assigns the component parameter value to a *private field* (`_expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="88ced-294">使用私有字段来维持它的内部切换状态。</span><span class="sxs-lookup"><span data-stu-id="88ced-294">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @_expanded)

    @if (_expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool _expanded;

    protected override void OnInitialized()
    {
        _expanded = Expanded;
    }

    private void Toggle()
    {
        _expanded = !_expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="88ced-295">分部类支持</span><span class="sxs-lookup"><span data-stu-id="88ced-295">Partial class support</span></span>

<span data-ttu-id="88ced-296">Razor 组件作为分部类生成。</span><span class="sxs-lookup"><span data-stu-id="88ced-296">Razor components are generated as partial classes.</span></span> <span data-ttu-id="88ced-297">使用以下方法之一创建 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-297">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="88ced-298">在 [`@code`](xref:mvc/views/razor#code) 块中使用单个文件中的 HTML 标记和 Razor 代码定义 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="88ced-298">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="88ced-299"> 模板使用此方法来定义其 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="88ced-299"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="88ced-300">C# 代码位于定义为分部类的代码隐藏文件中。</span><span class="sxs-lookup"><span data-stu-id="88ced-300">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="88ced-301">下面的示例显示了从 Blazor 模板生成的应用中具有 `@code` 块的默认 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="88ced-301">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="88ced-302">HTML 标记、Razor 代码和 C# 代码位于同一个文件中：</span><span class="sxs-lookup"><span data-stu-id="88ced-302">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="88ced-303">Counter.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-303">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="88ced-304">还可以使用具有分部类的代码隐藏文件创建 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-304">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="88ced-305">Counter.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-305">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="88ced-306">Counter.razor.cs  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-306">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="88ced-307">根据需要将任何所需的命名空间添加到分部类文件中。</span><span class="sxs-lookup"><span data-stu-id="88ced-307">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="88ced-308">Razor 组件使用的典型命名空间包括：</span><span class="sxs-lookup"><span data-stu-id="88ced-308">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="88ced-309">指定基类</span><span class="sxs-lookup"><span data-stu-id="88ced-309">Specify a base class</span></span>

<span data-ttu-id="88ced-310">[`@inherits`](xref:mvc/views/razor#inherits) 指令可用于指定组件的基类。</span><span class="sxs-lookup"><span data-stu-id="88ced-310">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="88ced-311">下面的示例演示组件如何继承基类 `BlazorRocksBase` 以提供组件的属性和方法。</span><span class="sxs-lookup"><span data-stu-id="88ced-311">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="88ced-312">基类应派生自 `ComponentBase`。</span><span class="sxs-lookup"><span data-stu-id="88ced-312">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="88ced-313">Pages/BlazorRocks.razor  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-313">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="88ced-314">BlazorRocksBase.cs  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-314">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="88ced-315">指定属性</span><span class="sxs-lookup"><span data-stu-id="88ced-315">Specify an attribute</span></span>

<span data-ttu-id="88ced-316">可以通过 [`@attribute`](xref:mvc/views/razor#attribute) 指令在 Razor 组件中指定属性。</span><span class="sxs-lookup"><span data-stu-id="88ced-316">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="88ced-317">下面的示例将 `[Authorize]` 属性应用于组件类：</span><span class="sxs-lookup"><span data-stu-id="88ced-317">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="88ced-318">导入组件</span><span class="sxs-lookup"><span data-stu-id="88ced-318">Import components</span></span>

<span data-ttu-id="88ced-319">使用 Razor 创建的组件的命名空间基于（按优先级顺序）：</span><span class="sxs-lookup"><span data-stu-id="88ced-319">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="88ced-320">Razor 文件 (.razor) 标记 (`@namespace BlazorSample.MyNamespace`) 中的 [`@namespace`](xref:mvc/views/razor#namespace) 指定内容  。</span><span class="sxs-lookup"><span data-stu-id="88ced-320">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="88ced-321">项目文件 (`<RootNamespace>BlazorSample</RootNamespace>`) 中项目的 `RootNamespace`。</span><span class="sxs-lookup"><span data-stu-id="88ced-321">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="88ced-322">项目名称，取自项目文件的文件名 (.csproj)，以及从项目根到组件的路径  。</span><span class="sxs-lookup"><span data-stu-id="88ced-322">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="88ced-323">例如，框架将 {PROJECT ROOT}/Pages/Index.razor (BlazorSample.csproj) 解析为命名空间 `BlazorSample.Pages`  。</span><span class="sxs-lookup"><span data-stu-id="88ced-323">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="88ced-324">组件遵循 C# 名称绑定规则。</span><span class="sxs-lookup"><span data-stu-id="88ced-324">Components follow C# name binding rules.</span></span> <span data-ttu-id="88ced-325">对于本示例中的 `Index` 组件，范围内的组件是所有组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-325">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="88ced-326">在同一文件夹 Pages 中  。</span><span class="sxs-lookup"><span data-stu-id="88ced-326">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="88ced-327">未显式指定其他命名空间的项目根中的组件。</span><span class="sxs-lookup"><span data-stu-id="88ced-327">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="88ced-328">使用 Razor 的 [`@using`](xref:mvc/views/razor#using) 指令将不同命名空间中定义的组件引入范围中。</span><span class="sxs-lookup"><span data-stu-id="88ced-328">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="88ced-329">如果 BlazorSample/Shared/ 文件夹中存在另一个组件 `NavMenu.razor`，则可以通过以下 `@using` 语句在 `Index.razor` 中使用该组件  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-329">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="88ced-330">还可以使用其完全限定的名称来引用组件，而不需要 [`@using`](xref:mvc/views/razor#using) 指令：</span><span class="sxs-lookup"><span data-stu-id="88ced-330">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="88ced-331">不支持 `global::` 限定。</span><span class="sxs-lookup"><span data-stu-id="88ced-331">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="88ced-332">不支持导入具有别名 `using` 语句的组件（例如，`@using Foo = Bar`）。</span><span class="sxs-lookup"><span data-stu-id="88ced-332">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="88ced-333">不支持部分限定名称。</span><span class="sxs-lookup"><span data-stu-id="88ced-333">Partially qualified names aren't supported.</span></span> <span data-ttu-id="88ced-334">例如，不支持使用 `<Shared.NavMenu></Shared.NavMenu>` 添加 `@using BlazorSample` 和引用 `NavMenu.razor`。</span><span class="sxs-lookup"><span data-stu-id="88ced-334">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="88ced-335">条件 HTML 元素属性</span><span class="sxs-lookup"><span data-stu-id="88ced-335">Conditional HTML element attributes</span></span>

<span data-ttu-id="88ced-336">HTML 元素属性基于 .NET 值有条件地呈现。</span><span class="sxs-lookup"><span data-stu-id="88ced-336">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="88ced-337">如果值为 `false` 或 `null`，则不会呈现属性。</span><span class="sxs-lookup"><span data-stu-id="88ced-337">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="88ced-338">如果值为 `true`，则以最小化呈现属性。</span><span class="sxs-lookup"><span data-stu-id="88ced-338">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="88ced-339">在下面的示例中，`IsCompleted` 确定是否在元素的标记中呈现 `checked`：</span><span class="sxs-lookup"><span data-stu-id="88ced-339">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="88ced-340">如果 `IsCompleted` 为 `true`，则复选框呈现为：</span><span class="sxs-lookup"><span data-stu-id="88ced-340">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="88ced-341">如果 `IsCompleted` 为 `false`，则复选框呈现为：</span><span class="sxs-lookup"><span data-stu-id="88ced-341">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="88ced-342">有关详细信息，请参阅 <xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="88ced-342">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="88ced-343">.NET 类型为 `bool` 时，某些 HTML 属性（如 [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)）无法正常运行。</span><span class="sxs-lookup"><span data-stu-id="88ced-343">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="88ced-344">在这些情况下，请使用 `string` 类型，而不是 `bool`。</span><span class="sxs-lookup"><span data-stu-id="88ced-344">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="88ced-345">原始 HTML</span><span class="sxs-lookup"><span data-stu-id="88ced-345">Raw HTML</span></span>

<span data-ttu-id="88ced-346">通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文字文本。</span><span class="sxs-lookup"><span data-stu-id="88ced-346">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="88ced-347">若要呈现原始 HTML，请将 HTML 内容包装在 `MarkupString` 值中。</span><span class="sxs-lookup"><span data-stu-id="88ced-347">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="88ced-348">将该值分析为 HTML 或 SVG，并插入到 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="88ced-348">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="88ced-349">呈现从任何不受信任的源构造的原始 HTML 存在安全风险，应避免  ！</span><span class="sxs-lookup"><span data-stu-id="88ced-349">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="88ced-350">下面的示例演示如何使用 `MarkupString` 类型向组件的呈现输出添加静态 HTML 内容块：</span><span class="sxs-lookup"><span data-stu-id="88ced-350">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="88ced-351">级联值和参数</span><span class="sxs-lookup"><span data-stu-id="88ced-351">Cascading values and parameters</span></span>

<span data-ttu-id="88ced-352">在某些情况下，使用[组件参数](#component-parameters)将数据从祖先组件流向子代组件不太方便，尤其是在有多个组件层时。</span><span class="sxs-lookup"><span data-stu-id="88ced-352">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="88ced-353">级联值和参数提供了一种方便的方法，使祖先组件为其所有子代组件提供值，从而解决了此问题。</span><span class="sxs-lookup"><span data-stu-id="88ced-353">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="88ced-354">级联值和参数还提供了一种协调组件的方法。</span><span class="sxs-lookup"><span data-stu-id="88ced-354">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="88ced-355">主题示例</span><span class="sxs-lookup"><span data-stu-id="88ced-355">Theme example</span></span>

<span data-ttu-id="88ced-356">在示例应用的以下示例中，`ThemeInfo` 类指定了要沿组件层次结构向下传递的主题信息，以便应用中给定部分内的所有按钮共享相同样式。</span><span class="sxs-lookup"><span data-stu-id="88ced-356">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="88ced-357">UIThemeClasses/ThemeInfo.cs  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-357">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="88ced-358">祖先组件可以使用级联值组件提供级联值。</span><span class="sxs-lookup"><span data-stu-id="88ced-358">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="88ced-359">`CascadingValue` 组件包装组件层次结构的子树，并向该子树内的所有组件提供单个值。</span><span class="sxs-lookup"><span data-stu-id="88ced-359">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="88ced-360">例如，示例应用将应用布局之一中的主题信息 (`ThemeInfo`) 指定为组成 `@Body` 属性布局正文的所有组件的级联参数。</span><span class="sxs-lookup"><span data-stu-id="88ced-360">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="88ced-361">在布局组件中为 `ButtonClass` 分配了 `btn-success` 的值。</span><span class="sxs-lookup"><span data-stu-id="88ced-361">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="88ced-362">任何子代组件都可以通过 `ThemeInfo` 级联对象使用此属性。</span><span class="sxs-lookup"><span data-stu-id="88ced-362">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="88ced-363">`CascadingValuesParametersLayout` 组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-363">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="88ced-364">为了使用级联值，组件使用 `[CascadingParameter]` 属性声明级联参数。</span><span class="sxs-lookup"><span data-stu-id="88ced-364">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="88ced-365">级联值按类型绑定到级联参数。</span><span class="sxs-lookup"><span data-stu-id="88ced-365">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="88ced-366">在示例应用中，`CascadingValuesParametersTheme` 组件将 `ThemeInfo` 级联值绑定到级联参数。</span><span class="sxs-lookup"><span data-stu-id="88ced-366">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="88ced-367">该参数用于为组件显示的按钮之一设置 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="88ced-367">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="88ced-368">`CascadingValuesParametersTheme` 组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-368">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="88ced-369">若要在同一子树内级联多个相同类型的值，请为每个 `CascadingValue` 组件及其相应的 `CascadingParameter` 提供唯一的 `Name` 字符串。</span><span class="sxs-lookup"><span data-stu-id="88ced-369">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="88ced-370">在下面的示例中，两个 `CascadingValue` 组件按名称级联 `MyCascadingType` 的不同实例：</span><span class="sxs-lookup"><span data-stu-id="88ced-370">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="88ced-371">在子代组件中，级联参数按名称从祖先组件中相应的级联值接收值：</span><span class="sxs-lookup"><span data-stu-id="88ced-371">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="88ced-372">TabSet 示例</span><span class="sxs-lookup"><span data-stu-id="88ced-372">TabSet example</span></span>

<span data-ttu-id="88ced-373">级联参数还允许组件跨组件层次结构进行协作。</span><span class="sxs-lookup"><span data-stu-id="88ced-373">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="88ced-374">例如，请考虑示例应用中的以下 TabSet 示例  。</span><span class="sxs-lookup"><span data-stu-id="88ced-374">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="88ced-375">该示例应用包含一个选项卡实现的 `ITab` 接口：</span><span class="sxs-lookup"><span data-stu-id="88ced-375">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="88ced-376">`CascadingValuesParametersTabSet` 组件使用 `TabSet` 组件，其中包含多个 `Tab` 组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-376">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="88ced-377">子 `Tab` 组件不会作为参数显式传递给 `TabSet`。</span><span class="sxs-lookup"><span data-stu-id="88ced-377">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="88ced-378">子 `Tab` 组件是 `TabSet` 的子内容的一部分。</span><span class="sxs-lookup"><span data-stu-id="88ced-378">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="88ced-379">但 `TabSet` 仍需要了解每个 `Tab` 组件，以便它可以呈现标头和活动选项卡。若要在不需要额外代码的情况下启用此协调，`TabSet` 组件可以将自身作为级联值提供，然后由子代 `Tab` 组件选取  。</span><span class="sxs-lookup"><span data-stu-id="88ced-379">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="88ced-380">`TabSet` 组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-380">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="88ced-381">子代 `Tab` 组件将包含的 `TabSet` 作为级联参数捕获，因此 `Tab` 组件会将其自身添加到 `TabSet` 并在处于活动状态的选项卡上进行协调。</span><span class="sxs-lookup"><span data-stu-id="88ced-381">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="88ced-382">`Tab` 组件：</span><span class="sxs-lookup"><span data-stu-id="88ced-382">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="88ced-383">Razor 模板</span><span class="sxs-lookup"><span data-stu-id="88ced-383">Razor templates</span></span>

<span data-ttu-id="88ced-384">可以使用 Razor 模板语法来定义呈现片段。</span><span class="sxs-lookup"><span data-stu-id="88ced-384">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="88ced-385">Razor 模板是一种定义 UI 代码片段的方法，请使用以下格式：</span><span class="sxs-lookup"><span data-stu-id="88ced-385">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="88ced-386">下面的示例演示如何在组件中指定 `RenderFragment` 和 `RenderFragment<T>` 值并直接呈现模板。</span><span class="sxs-lookup"><span data-stu-id="88ced-386">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="88ced-387">还可以将呈现片段作为参数传递给[模板化组件](xref:blazor/templated-components)。</span><span class="sxs-lookup"><span data-stu-id="88ced-387">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="88ced-388">以上代码的呈现输出：</span><span class="sxs-lookup"><span data-stu-id="88ced-388">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="88ced-389">可缩放的向量图形 (SVG) 图像</span><span class="sxs-lookup"><span data-stu-id="88ced-389">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="88ced-390">由于 Blazor 呈现 HTML，因此通过 `<img>` 标记支持浏览器支持的图像，包括可缩放的矢量图形 (SVG) 图像(.svg)  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-390">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="88ced-391">同样，样式表文件 (.css) 的 CSS 规则支持 SVG 图像  ：</span><span class="sxs-lookup"><span data-stu-id="88ced-391">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="88ced-392">但是，并非在所有情况下都支持内联的 SVG 标记。</span><span class="sxs-lookup"><span data-stu-id="88ced-392">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="88ced-393">如果将 `<svg>` 标记直接放入组件文件 (.razor)，则支持基本图像呈现，但很多高级场景尚不受支持  。</span><span class="sxs-lookup"><span data-stu-id="88ced-393">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="88ced-394">例如，当前未遵循 `<use>` 标记，并且 `@bind` 不能与某些 SVG 标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="88ced-394">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="88ced-395">我们预计会在将来的版本中解决这些限制。</span><span class="sxs-lookup"><span data-stu-id="88ced-395">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88ced-396">其他资源</span><span class="sxs-lookup"><span data-stu-id="88ced-396">Additional resources</span></span>

* <span data-ttu-id="88ced-397"><xref:security/blazor/server> &ndash; 包含有关生成必须应对资源耗尽的 Blazor 服务器应用的指南。</span><span class="sxs-lookup"><span data-stu-id="88ced-397"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
