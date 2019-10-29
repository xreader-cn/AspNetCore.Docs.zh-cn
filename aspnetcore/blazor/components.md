---
title: 创建和使用 ASP.NET Core Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件，包括如何绑定到数据、处理事件和管理组件生命周期。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/21/2019
uid: blazor/components
ms.openlocfilehash: 8c228b168cdbd58928ef3f57ff26bc86e8dfc1ba
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033979"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="1d20f-103">创建和使用 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="1d20f-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="1d20f-104">作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1d20f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1d20f-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1d20f-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1d20f-106">Blazor 应用是使用*组件*生成的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="1d20f-107">组件是自包含的用户界面（UI）块，如页、对话框或窗体。</span><span class="sxs-lookup"><span data-stu-id="1d20f-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="1d20f-108">组件包含插入数据或响应 UI 事件所需的 HTML 标记和处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="1d20f-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="1d20f-109">组件非常灵活且轻型。</span><span class="sxs-lookup"><span data-stu-id="1d20f-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="1d20f-110">它们可以嵌套、重复使用和在项目之间共享。</span><span class="sxs-lookup"><span data-stu-id="1d20f-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="1d20f-111">组件类</span><span class="sxs-lookup"><span data-stu-id="1d20f-111">Component classes</span></span>

<span data-ttu-id="1d20f-112">在[razor](xref:mvc/views/razor)组件文件（*razor*）中，使用和 HTML 标记的C#组合实现了组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="1d20f-113">Blazor 中的组件正式称为*Razor 组件*。</span><span class="sxs-lookup"><span data-stu-id="1d20f-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="1d20f-114">组件的名称必须以大写字符开头。</span><span class="sxs-lookup"><span data-stu-id="1d20f-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="1d20f-115">例如， *MyCoolComponent*是有效的，并且*MyCoolComponent*无效。</span><span class="sxs-lookup"><span data-stu-id="1d20f-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="1d20f-116">使用 HTML 定义组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="1d20f-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="1d20f-117">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="1d20f-118">在编译应用程序时，会将 HTML 标记C#和呈现逻辑转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="1d20f-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="1d20f-119">生成的类的名称与文件的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="1d20f-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="1d20f-120">组件类的成员在 `@code` 块中定义。</span><span class="sxs-lookup"><span data-stu-id="1d20f-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="1d20f-121">在 `@code` 块中，组件状态（属性、字段）是通过事件处理方法或定义其他组件逻辑来指定的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="1d20f-122">允许多个 `@code` 块。</span><span class="sxs-lookup"><span data-stu-id="1d20f-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="1d20f-123">在 ASP.NET Core 3.0 的先前预览中，`@functions` 块用于与 Razor 组件中 `@code` 块相同的目的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-123">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="1d20f-124">`@functions` 块继续在 Razor 组件中运行，但我们建议在 ASP.NET Core 3.0 Preview 6 或更高版本中使用 `@code` 块。</span><span class="sxs-lookup"><span data-stu-id="1d20f-124">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="1d20f-125">可以使用C#以 `@` 开头的表达式将组件成员作为组件的呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="1d20f-125">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="1d20f-126">例如， C#字段通过在字段名称 `@` 之前进行呈现。</span><span class="sxs-lookup"><span data-stu-id="1d20f-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="1d20f-127">下面的示例计算并呈现：</span><span class="sxs-lookup"><span data-stu-id="1d20f-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="1d20f-128">`_headingFontStyle` `font-style`的 CSS 属性值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="1d20f-129">`_headingText` 到 `<h1>` 元素的内容。</span><span class="sxs-lookup"><span data-stu-id="1d20f-129">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="1d20f-130">最初呈现组件后，组件会为响应事件而重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="1d20f-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="1d20f-131">然后，Blazor 将新的呈现树与上一个树进行比较，并将所有修改应用于浏览器的文档对象模型（DOM）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="1d20f-132">组件是普通C#类，可以放置在项目中的任何位置。</span><span class="sxs-lookup"><span data-stu-id="1d20f-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="1d20f-133">生成网页的组件通常位于*Pages*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1d20f-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="1d20f-134">非页组件通常放置在*共享*文件夹中或添加到项目的自定义文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1d20f-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="1d20f-135">若要使用自定义文件夹，请将自定义文件夹的命名空间添加到父组件或应用程序的 *_Imports*文件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-135">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="1d20f-136">例如，以下命名空间使*组件*文件夹中的组件在应用程序的根命名空间 `WebApplication` 时可用：</span><span class="sxs-lookup"><span data-stu-id="1d20f-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="1d20f-137">将组件集成到 Razor Pages 和 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="1d20f-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="1d20f-138">将组件用于现有 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="1d20f-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="1d20f-139">无需重写现有页面或视图即可使用 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="1d20f-140">呈现页面或视图时，组件将同时预呈现。</span><span class="sxs-lookup"><span data-stu-id="1d20f-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="1d20f-141">若要从页面或视图呈现组件，请使用 `RenderComponentAsync<TComponent>` HTML 帮助器方法：</span><span class="sxs-lookup"><span data-stu-id="1d20f-141">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="1d20f-142">尽管页面和视图可以使用组件，但不是这样。</span><span class="sxs-lookup"><span data-stu-id="1d20f-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="1d20f-143">组件不能使用视图和特定于页的方案，如分部视图和节。</span><span class="sxs-lookup"><span data-stu-id="1d20f-143">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="1d20f-144">若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解为一个组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-144">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="1d20f-145">有关如何呈现组件和在 Blazor Server 应用程序中管理组件状态的详细信息，请参阅 <xref:blazor/hosting-models> 文章。</span><span class="sxs-lookup"><span data-stu-id="1d20f-145">For more information on how components are rendered and component state is managed in Blazor Server apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="1d20f-146">使用组件</span><span class="sxs-lookup"><span data-stu-id="1d20f-146">Use components</span></span>

<span data-ttu-id="1d20f-147">组件可以通过使用 HTML 元素语法声明组件来包含其他组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-147">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="1d20f-148">使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。</span><span class="sxs-lookup"><span data-stu-id="1d20f-148">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="1d20f-149">属性绑定区分大小写。</span><span class="sxs-lookup"><span data-stu-id="1d20f-149">Attribute binding is case sensitive.</span></span> <span data-ttu-id="1d20f-150">例如，`@bind` 有效，`@Bind` 无效。</span><span class="sxs-lookup"><span data-stu-id="1d20f-150">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="1d20f-151">*Index*中的以下标记会呈现 `HeadingComponent` 实例：</span><span class="sxs-lookup"><span data-stu-id="1d20f-151">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="1d20f-152">*组件/HeadingComponent*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-152">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="1d20f-153">如果某个组件包含一个 HTML 元素，该元素的首字母大写字母与组件名称不匹配，则会发出警告，指示该元素具有意外的名称。</span><span class="sxs-lookup"><span data-stu-id="1d20f-153">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="1d20f-154">为组件命名空间添加 `@using` 语句会使组件可用，从而消除了警告。</span><span class="sxs-lookup"><span data-stu-id="1d20f-154">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="1d20f-155">组件参数</span><span class="sxs-lookup"><span data-stu-id="1d20f-155">Component parameters</span></span>

<span data-ttu-id="1d20f-156">组件可以具有*组件参数*，这些参数是使用组件类上的公共属性和 `[Parameter]` 特性定义的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-156">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="1d20f-157">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-157">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="1d20f-158">*组件/ChildComponent*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-158">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="1d20f-159">在下面的示例中，`ParentComponent` 设置 `ChildComponent` 的 `Title` 属性的值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-159">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="1d20f-160">*Pages/ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-160">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="1d20f-161">子内容</span><span class="sxs-lookup"><span data-stu-id="1d20f-161">Child content</span></span>

<span data-ttu-id="1d20f-162">组件可以设置另一个组件的内容。</span><span class="sxs-lookup"><span data-stu-id="1d20f-162">Components can set the content of another component.</span></span> <span data-ttu-id="1d20f-163">分配组件提供用于指定接收组件的标记之间的内容。</span><span class="sxs-lookup"><span data-stu-id="1d20f-163">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="1d20f-164">在下面的示例中，`ChildComponent` 具有一个表示 `RenderFragment` 的 `ChildContent` 属性，该属性表示要呈现的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="1d20f-164">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="1d20f-165">`ChildContent` 的值放置在应呈现内容的组件标记中。</span><span class="sxs-lookup"><span data-stu-id="1d20f-165">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="1d20f-166">`ChildContent` 的值从父组件接收，并呈现在启动面板的 `panel-body`中。</span><span class="sxs-lookup"><span data-stu-id="1d20f-166">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="1d20f-167">*组件/ChildComponent*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-167">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="1d20f-168">接收 `RenderFragment` 内容的属性必须按约定命名为 `ChildContent`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-168">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="1d20f-169">以下 `ParentComponent` 可以通过将内容置于 `<ChildComponent>` 标记中来提供用于呈现 `ChildComponent` 的内容。</span><span class="sxs-lookup"><span data-stu-id="1d20f-169">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="1d20f-170">*Pages/ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-170">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="1d20f-171">Attribute 展开和任意参数</span><span class="sxs-lookup"><span data-stu-id="1d20f-171">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="1d20f-172">除了组件的声明参数外，组件还可以捕获和呈现附加属性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-172">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="1d20f-173">其他属性可以在字典中捕获，然后在使用[@attributes](xref:mvc/views/razor#attributes) Razor 指令呈现组件时， *splatted*到元素上。</span><span class="sxs-lookup"><span data-stu-id="1d20f-173">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="1d20f-174">此方案在定义生成支持各种自定义项的标记元素的组件时非常有用。</span><span class="sxs-lookup"><span data-stu-id="1d20f-174">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="1d20f-175">例如，为支持多个参数的 `<input>` 分别定义属性可能会很繁琐。</span><span class="sxs-lookup"><span data-stu-id="1d20f-175">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="1d20f-176">在下面的示例中，第一个 `<input>` 元素（`id="useIndividualParams"`）使用单个组件参数，第二个 `<input>` 元素（`id="useAttributesDict"`）使用属性展开：</span><span class="sxs-lookup"><span data-stu-id="1d20f-176">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```cshtml
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

<span data-ttu-id="1d20f-177">参数的类型必须实现 `IEnumerable<KeyValuePair<string, object>>` 替换字符串键。</span><span class="sxs-lookup"><span data-stu-id="1d20f-177">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="1d20f-178">在此方案中，还可以选择使用 `IReadOnlyDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-178">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="1d20f-179">使用这两种方法呈现的 `<input>` 元素相同：</span><span class="sxs-lookup"><span data-stu-id="1d20f-179">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="1d20f-180">若要接受任意属性，请使用 `[Parameter]` 特性定义组件参数，并将 `CaptureUnmatchedValues` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="1d20f-180">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="1d20f-181">`[Parameter]` 上的 `CaptureUnmatchedValues` 属性允许参数匹配所有不匹配任何其他参数的属性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-181">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="1d20f-182">组件只能使用 `CaptureUnmatchedValues` 定义单个参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-182">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="1d20f-183">与 `CaptureUnmatchedValues` 一起使用的属性类型必须从具有字符串键的 `Dictionary<string, object>` 中赋值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-183">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="1d20f-184">此方案中也可以选择 `IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-184">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="1d20f-185">相对于元素特性位置 `@attributes` 的位置很重要。</span><span class="sxs-lookup"><span data-stu-id="1d20f-185">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="1d20f-186">当在元素上 splatted `@attributes` 时，将从右到左（从上到下）处理特性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-186">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="1d20f-187">请考虑以下示例：使用 `Child` 组件的组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-187">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="1d20f-188">*ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-188">*ParentComponent.razor*:</span></span>

```cshtml
<ChildComponent extra="10" />
```

<span data-ttu-id="1d20f-189">*ChildComponent*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-189">*ChildComponent.razor*:</span></span>

```cshtml
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="1d20f-190">`Child` 组件的 `extra` 属性设置为 `@attributes`的右侧。</span><span class="sxs-lookup"><span data-stu-id="1d20f-190">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="1d20f-191">当通过附加属性传递时，`Parent` 组件的呈现 `<div>` 包含 `extra="5"`，因为属性从右到左处理（从上到第一）：</span><span class="sxs-lookup"><span data-stu-id="1d20f-191">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="1d20f-192">在下面的示例中，`Child` 组件的 `<div>`中反转 `extra` 和 `@attributes` 的顺序：</span><span class="sxs-lookup"><span data-stu-id="1d20f-192">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="1d20f-193">*ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-193">*ParentComponent.razor*:</span></span>

```cshtml
<ChildComponent extra="10" />
```

<span data-ttu-id="1d20f-194">*ChildComponent*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-194">*ChildComponent.razor*:</span></span>

```cshtml
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="1d20f-195">`Parent` 组件中呈现的 `<div>` 包含通过附加属性传递的 `extra="10"`：</span><span class="sxs-lookup"><span data-stu-id="1d20f-195">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="data-binding"></a><span data-ttu-id="1d20f-196">数据绑定</span><span class="sxs-lookup"><span data-stu-id="1d20f-196">Data binding</span></span>

<span data-ttu-id="1d20f-197">对组件和 DOM 元素的数据绑定都是通过[@bind](xref:mvc/views/razor#bind)属性来完成的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-197">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="1d20f-198">下面的示例将 `CurrentValue` 属性绑定到文本框的值：</span><span class="sxs-lookup"><span data-stu-id="1d20f-198">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```cshtml
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1d20f-199">当文本框失去焦点时，会更新该属性的值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-199">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="1d20f-200">仅当呈现组件时，才会在 UI 中更新文本框，而不是响应更改属性值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-200">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="1d20f-201">由于在事件处理程序代码执行后组件会自行呈现，因此在事件处理程序触发后，属性更新*通常*会立即反映在 UI 中。</span><span class="sxs-lookup"><span data-stu-id="1d20f-201">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="1d20f-202">结合使用 `@bind` 与 `CurrentValue` 属性（`<input @bind="CurrentValue" />`）本质上等效于以下内容：</span><span class="sxs-lookup"><span data-stu-id="1d20f-202">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1d20f-203">呈现组件时，输入元素的 `value` 来自 `CurrentValue` 属性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-203">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="1d20f-204">当用户在文本框中键入内容并更改元素焦点时，将激发 `onchange` 事件并将 `CurrentValue` 属性设置为更改的值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-204">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="1d20f-205">事实上，代码生成更复杂，因为 `@bind` 处理执行类型转换的情况。</span><span class="sxs-lookup"><span data-stu-id="1d20f-205">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="1d20f-206">原则上，`@bind` 将表达式的当前值与 `value` 属性相关联，并使用注册的处理程序来处理更改。</span><span class="sxs-lookup"><span data-stu-id="1d20f-206">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="1d20f-207">除了使用 `@bind` 语法处理 `onchange` 事件外，还可以使用其他事件来绑定属性或字段，方法是使用 `event` 参数指定[@bind-value](xref:mvc/views/razor#bind)属性（[@bind-value:event](xref:mvc/views/razor#bind)）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-207">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="1d20f-208">下面的示例绑定了 `oninput` 事件的 `CurrentValue` 属性：</span><span class="sxs-lookup"><span data-stu-id="1d20f-208">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1d20f-209">与 `onchange` 不同，后者在元素失去焦点时激发，`oninput` 在文本框的值更改时激发。</span><span class="sxs-lookup"><span data-stu-id="1d20f-209">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="1d20f-210">**不可分析值**</span><span class="sxs-lookup"><span data-stu-id="1d20f-210">**Unparsable values**</span></span>

<span data-ttu-id="1d20f-211">当用户向数据绑定元素提供无法分析的值时，触发绑定事件时，无法分析的值将自动恢复为其以前的值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-211">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="1d20f-212">请参考以下方案：</span><span class="sxs-lookup"><span data-stu-id="1d20f-212">Consider the following scenario:</span></span>

* <span data-ttu-id="1d20f-213">`<input>` 元素绑定到 `int` 类型，其初始值为 `123`：</span><span class="sxs-lookup"><span data-stu-id="1d20f-213">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="1d20f-214">用户将元素的值更新为该页 `123.45`，并更改元素焦点。</span><span class="sxs-lookup"><span data-stu-id="1d20f-214">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="1d20f-215">在上述方案中，将元素的值还原为 `123`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-215">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="1d20f-216">如果拒绝值 `123.45` 以便于 `123` 的原始值，则用户知道其值不被接受。</span><span class="sxs-lookup"><span data-stu-id="1d20f-216">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="1d20f-217">默认情况下，绑定适用于元素的 `onchange` 事件（`@bind="{PROPERTY OR FIELD}"`）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-217">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="1d20f-218">使用 `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` 设置不同的事件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-218">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="1d20f-219">对于 `oninput` 事件（`@bind-value:event="oninput"`），重新确定会在引入了可分析值的任何击键之后发生。</span><span class="sxs-lookup"><span data-stu-id="1d20f-219">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="1d20f-220">当以 `int` 绑定类型为 `oninput` 事件定位时，将阻止用户键入 `.` 字符。</span><span class="sxs-lookup"><span data-stu-id="1d20f-220">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="1d20f-221">会立即删除 `.` 字符，因此用户会立即收到仅允许整数的反馈。</span><span class="sxs-lookup"><span data-stu-id="1d20f-221">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="1d20f-222">在某些情况下，在 `oninput` 事件上恢复该值的情况并不理想，例如当用户应允许清除无法解析的 `<input>` 值时。</span><span class="sxs-lookup"><span data-stu-id="1d20f-222">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="1d20f-223">替代项包括：</span><span class="sxs-lookup"><span data-stu-id="1d20f-223">Alternatives include:</span></span>

* <span data-ttu-id="1d20f-224">不要使用 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-224">Don't use the `oninput` event.</span></span> <span data-ttu-id="1d20f-225">使用默认 `onchange` 事件（`@bind="{PROPERTY OR FIELD}"`），其中无效值直到元素失去焦点时才会被还原。</span><span class="sxs-lookup"><span data-stu-id="1d20f-225">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="1d20f-226">绑定到可为 null 的类型，例如 `int?` 或 `string`，并提供自定义逻辑来处理无效条目。</span><span class="sxs-lookup"><span data-stu-id="1d20f-226">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="1d20f-227">使用[窗体验证组件](xref:blazor/forms-validation)，如 `InputNumber` 或 `InputDate`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-227">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="1d20f-228">窗体验证组件具有用于管理无效输入的内置支持。</span><span class="sxs-lookup"><span data-stu-id="1d20f-228">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="1d20f-229">窗体验证组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-229">Form validation components:</span></span>
  * <span data-ttu-id="1d20f-230">允许用户提供无效输入，并在关联的 `EditContext` 上收到验证错误。</span><span class="sxs-lookup"><span data-stu-id="1d20f-230">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="1d20f-231">在 UI 中显示验证错误，不干扰用户输入其他 webform 数据。</span><span class="sxs-lookup"><span data-stu-id="1d20f-231">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="1d20f-232">**全球化**</span><span class="sxs-lookup"><span data-stu-id="1d20f-232">**Globalization**</span></span>

<span data-ttu-id="1d20f-233">`@bind` 值的格式设置为显示，并使用当前区域性的规则进行分析。</span><span class="sxs-lookup"><span data-stu-id="1d20f-233">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="1d20f-234">可以从 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 属性访问当前区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-234">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="1d20f-235">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)用于以下字段类型（`<input type="{TYPE}" />`）：</span><span class="sxs-lookup"><span data-stu-id="1d20f-235">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="1d20f-236">上述字段类型：</span><span class="sxs-lookup"><span data-stu-id="1d20f-236">The preceding field types:</span></span>

* <span data-ttu-id="1d20f-237">使用其基于浏览器的适当格式规则显示。</span><span class="sxs-lookup"><span data-stu-id="1d20f-237">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="1d20f-238">不能包含自由格式的文本。</span><span class="sxs-lookup"><span data-stu-id="1d20f-238">Can't contain free-form text.</span></span>
* <span data-ttu-id="1d20f-239">基于浏览器的实现提供用户交互特性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-239">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="1d20f-240">以下字段类型具有特定的格式要求，Blazor 当前不支持它们，因为它们不受所有主要浏览器的支持：</span><span class="sxs-lookup"><span data-stu-id="1d20f-240">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="1d20f-241">`@bind` 支持 `@bind:culture` 参数，以提供 <xref:System.Globalization.CultureInfo?displayProperty=fullName> 用于分析和设置值的格式。</span><span class="sxs-lookup"><span data-stu-id="1d20f-241">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="1d20f-242">使用 `date` 和 `number` 字段类型时，不建议指定区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-242">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="1d20f-243">`date` 和 `number` 具有提供所需区域性的内置 Blazor 支持。</span><span class="sxs-lookup"><span data-stu-id="1d20f-243">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="1d20f-244">有关如何设置用户的区域性的信息，请参阅[本地化](#localization)部分。</span><span class="sxs-lookup"><span data-stu-id="1d20f-244">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="1d20f-245">**格式字符串**</span><span class="sxs-lookup"><span data-stu-id="1d20f-245">**Format strings**</span></span>

<span data-ttu-id="1d20f-246">数据绑定使用[@bind:format](xref:mvc/views/razor#bind)<xref:System.DateTime> 格式字符串。</span><span class="sxs-lookup"><span data-stu-id="1d20f-246">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="1d20f-247">现在不能使用其他格式的表达式，如货币或数字格式。</span><span class="sxs-lookup"><span data-stu-id="1d20f-247">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="1d20f-248">在前面的代码中，`<input>` 元素的字段类型（`type`）默认为 `text`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-248">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="1d20f-249">支持 `@bind:format` 绑定以下 .NET 类型：</span><span class="sxs-lookup"><span data-stu-id="1d20f-249">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="1d20f-250"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="1d20f-250"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="1d20f-251"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="1d20f-251"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="1d20f-252">`@bind:format` 特性指定要应用于 `<input>` 元素的 `value` 的日期格式。</span><span class="sxs-lookup"><span data-stu-id="1d20f-252">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="1d20f-253">此格式还用于分析 `onchange` 事件发生时的值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-253">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="1d20f-254">不建议为 `date` 字段类型指定格式，因为 Blazor 具有设置日期格式的内置支持。</span><span class="sxs-lookup"><span data-stu-id="1d20f-254">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="1d20f-255">**组件参数**</span><span class="sxs-lookup"><span data-stu-id="1d20f-255">**Component parameters**</span></span>

<span data-ttu-id="1d20f-256">绑定可识别组件参数，其中 `@bind-{property}` 可跨组件绑定属性值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-256">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="1d20f-257">以下子组件（`ChildComponent`）具有 `Year` 组件参数和 `YearChanged` 回调：</span><span class="sxs-lookup"><span data-stu-id="1d20f-257">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="1d20f-258">[EventCallback](#eventcallback)部分说明了 `EventCallback<T>`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-258">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="1d20f-259">以下父组件使用 `ChildComponent`，并将 `ParentYear` 参数从父级绑定到子组件上的 `Year` 参数：</span><span class="sxs-lookup"><span data-stu-id="1d20f-259">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="1d20f-260">加载 `ParentComponent` 会生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="1d20f-260">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="1d20f-261">如果通过在 `ParentComponent` 中选择按钮更改了 `ParentYear` 属性的值，则将更新 `ChildComponent` 的 `Year` 属性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-261">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="1d20f-262">当 `ParentComponent` 重新呈现时，用户界面中将 `Year` 的新值：</span><span class="sxs-lookup"><span data-stu-id="1d20f-262">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="1d20f-263">`Year` 参数是可绑定的，因为它具有与 `Year` 参数类型相匹配的伴随 `YearChanged` 事件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-263">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="1d20f-264">按照约定，`<ChildComponent @bind-Year="ParentYear" />` 实质上等同于编写：</span><span class="sxs-lookup"><span data-stu-id="1d20f-264">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="1d20f-265">通常，可以使用 `@bind-property:event` 特性将属性绑定到相应的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="1d20f-265">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="1d20f-266">例如，可以使用以下两个属性将属性 `MyProp` 绑定到 `MyEventHandler`：</span><span class="sxs-lookup"><span data-stu-id="1d20f-266">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="1d20f-267">事件处理</span><span class="sxs-lookup"><span data-stu-id="1d20f-267">Event handling</span></span>

<span data-ttu-id="1d20f-268">Razor 组件提供事件处理功能。</span><span class="sxs-lookup"><span data-stu-id="1d20f-268">Razor components provide event handling features.</span></span> <span data-ttu-id="1d20f-269">对于名为 `on{event}` 的 HTML 元素特性（例如，`onclick` 和 `onsubmit`）和委托类型的值，Razor 组件将属性的值视为事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="1d20f-269">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="1d20f-270">特性的名称始终[@on {event}](xref:mvc/views/razor#onevent)格式。</span><span class="sxs-lookup"><span data-stu-id="1d20f-270">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="1d20f-271">下面的代码在用户界面中选择按钮时调用 `UpdateHeading` 方法：</span><span class="sxs-lookup"><span data-stu-id="1d20f-271">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="1d20f-272">以下代码会在 UI 中的复选框发生更改时调用 `CheckChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="1d20f-272">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="1d20f-273">事件处理程序也可以是异步的，并返回 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="1d20f-273">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="1d20f-274">无需手动调用 `StateHasChanged()`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-274">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="1d20f-275">异常在发生时进行记录。</span><span class="sxs-lookup"><span data-stu-id="1d20f-275">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="1d20f-276">在下面的示例中，当选择按钮时，将异步调用 `UpdateHeading`：</span><span class="sxs-lookup"><span data-stu-id="1d20f-276">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="1d20f-277">事件参数类型</span><span class="sxs-lookup"><span data-stu-id="1d20f-277">Event argument types</span></span>

<span data-ttu-id="1d20f-278">对于某些事件，允许使用事件参数类型。</span><span class="sxs-lookup"><span data-stu-id="1d20f-278">For some events, event argument types are permitted.</span></span> <span data-ttu-id="1d20f-279">如果不需要访问这些事件类型之一，则方法调用中不需要这样做。</span><span class="sxs-lookup"><span data-stu-id="1d20f-279">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="1d20f-280">下表显示了支持的 `EventArgs`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-280">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="1d20f-281">Event — 事件</span><span class="sxs-lookup"><span data-stu-id="1d20f-281">Event</span></span> | <span data-ttu-id="1d20f-282">实例</span><span class="sxs-lookup"><span data-stu-id="1d20f-282">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="1d20f-283">剪贴板</span><span class="sxs-lookup"><span data-stu-id="1d20f-283">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="1d20f-284">入</span><span class="sxs-lookup"><span data-stu-id="1d20f-284">Drag</span></span>             | <span data-ttu-id="1d20f-285">`DragEventArgs` &ndash; `DataTransfer` 和 `DataTransferItem` 保存拖动的项数据。</span><span class="sxs-lookup"><span data-stu-id="1d20f-285">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="1d20f-286">Error</span><span class="sxs-lookup"><span data-stu-id="1d20f-286">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="1d20f-287">焦点</span><span class="sxs-lookup"><span data-stu-id="1d20f-287">Focus</span></span>            | <span data-ttu-id="1d20f-288">`FocusEventArgs` &ndash; 不包括对 `relatedTarget` 的支持。</span><span class="sxs-lookup"><span data-stu-id="1d20f-288">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="1d20f-289">`<input>` 已更改</span><span class="sxs-lookup"><span data-stu-id="1d20f-289">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="1d20f-290">键盘</span><span class="sxs-lookup"><span data-stu-id="1d20f-290">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="1d20f-291">鼠标</span><span class="sxs-lookup"><span data-stu-id="1d20f-291">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="1d20f-292">鼠标指针</span><span class="sxs-lookup"><span data-stu-id="1d20f-292">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="1d20f-293">鼠标滚轮</span><span class="sxs-lookup"><span data-stu-id="1d20f-293">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="1d20f-294">进度</span><span class="sxs-lookup"><span data-stu-id="1d20f-294">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="1d20f-295">触控</span><span class="sxs-lookup"><span data-stu-id="1d20f-295">Touch</span></span>            | <span data-ttu-id="1d20f-296">`TouchEventArgs` &ndash; `TouchPoint` 表示触摸敏感设备上的单个联系点。</span><span class="sxs-lookup"><span data-stu-id="1d20f-296">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="1d20f-297">有关上表中事件的属性和事件处理行为的信息，请参阅[引用源中的 EventArgs 类（aspnet/AspNetCore release/3.0 分支）](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="1d20f-297">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="1d20f-298">Lambda 表达式</span><span class="sxs-lookup"><span data-stu-id="1d20f-298">Lambda expressions</span></span>

<span data-ttu-id="1d20f-299">还可以使用 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="1d20f-299">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="1d20f-300">通常可以很方便地关闭其他值，如在循环访问一组元素时。</span><span class="sxs-lookup"><span data-stu-id="1d20f-300">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="1d20f-301">下面的示例创建了三个按钮，其中每个按钮都调用 `UpdateHeading` 在 UI 中选择时传递事件参数（`MouseEventArgs`）和其按钮号（`buttonNumber`）：</span><span class="sxs-lookup"><span data-stu-id="1d20f-301">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="1d20f-302">不要直接在 lambda 表达式**中使用 `for`** 循环中的循环变量（`i`）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-302">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="1d20f-303">否则，所有 lambda 表达式都将使用相同的变量，导致 `i` 的值在所有 lambda 中都相同。</span><span class="sxs-lookup"><span data-stu-id="1d20f-303">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="1d20f-304">始终捕获其在本地变量中的值（在前面的示例中 `buttonNumber`），然后使用它。</span><span class="sxs-lookup"><span data-stu-id="1d20f-304">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="1d20f-305">EventCallback</span><span class="sxs-lookup"><span data-stu-id="1d20f-305">EventCallback</span></span>

<span data-ttu-id="1d20f-306">使用嵌套组件的常见方案是，需要在子组件事件发生时运行父组件的方法 &mdash;for 例如，当子组件中发生 `onclick` 事件时。</span><span class="sxs-lookup"><span data-stu-id="1d20f-306">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="1d20f-307">若要跨组件公开事件，请使用 `EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-307">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="1d20f-308">父组件可将回调方法分配给子组件的 `EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-308">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="1d20f-309">示例应用中的 `ChildComponent` 演示如何设置按钮的 `onclick` 处理程序，以便从示例的 `ParentComponent` 接收 `EventCallback` 委托。</span><span class="sxs-lookup"><span data-stu-id="1d20f-309">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="1d20f-310">`EventCallback` 是使用 `MouseEventArgs`键入的，这适用于来自外围设备的 `onclick` 事件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-310">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="1d20f-311">`ParentComponent` 将子级的 `EventCallback<T>` 设置为其 `ShowMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="1d20f-311">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="1d20f-312">在 `ChildComponent` 中选择该按钮时：</span><span class="sxs-lookup"><span data-stu-id="1d20f-312">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="1d20f-313">调用 `ParentComponent` 的 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="1d20f-313">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="1d20f-314">`messageText` 会更新并显示在 `ParentComponent` 中。</span><span class="sxs-lookup"><span data-stu-id="1d20f-314">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="1d20f-315">回调的方法（`ShowMessage`）中不需要调用 `StateHasChanged`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-315">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="1d20f-316">`StateHasChanged` 会自动调用以诸如此类 `ParentComponent`，就像子事件在子中执行的事件处理程序中 rerendering。</span><span class="sxs-lookup"><span data-stu-id="1d20f-316">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="1d20f-317">`EventCallback` 和 `EventCallback<T>` 允许异步委托。</span><span class="sxs-lookup"><span data-stu-id="1d20f-317">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="1d20f-318">`EventCallback<T>` 为强类型，并且需要特定的参数类型。</span><span class="sxs-lookup"><span data-stu-id="1d20f-318">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="1d20f-319">`EventCallback` 弱类型化，并允许任何参数类型。</span><span class="sxs-lookup"><span data-stu-id="1d20f-319">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="1d20f-320">使用 `InvokeAsync` 调用 `EventCallback` 或 `EventCallback<T>`，并等待 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="1d20f-320">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="1d20f-321">使用 `EventCallback` 并 `EventCallback<T>` 进行事件处理和绑定组件参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-321">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="1d20f-322">优先于 `EventCallback` 的强类型 `EventCallback<T>`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-322">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="1d20f-323">`EventCallback<T>` 向组件用户提供更好的错误反馈。</span><span class="sxs-lookup"><span data-stu-id="1d20f-323">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="1d20f-324">与其他 UI 事件处理程序类似，指定事件参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-324">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="1d20f-325">当没有任何值传递到回调时，使用 `EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-325">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="chained-bind"></a><span data-ttu-id="1d20f-326">链式绑定</span><span class="sxs-lookup"><span data-stu-id="1d20f-326">Chained bind</span></span>

<span data-ttu-id="1d20f-327">常见的情况是将数据绑定参数链接到组件输出中的页元素。</span><span class="sxs-lookup"><span data-stu-id="1d20f-327">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="1d20f-328">此方案称为*链接绑定*，因为多个级别的绑定同时发生。</span><span class="sxs-lookup"><span data-stu-id="1d20f-328">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="1d20f-329">无法使用页面元素中的 `@bind` 语法实现链式绑定。</span><span class="sxs-lookup"><span data-stu-id="1d20f-329">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="1d20f-330">必须单独指定事件处理程序和值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-330">The event handler and value must be specified separately.</span></span> <span data-ttu-id="1d20f-331">但是，父组件可以将 `@bind` 语法与组件的参数一起使用。</span><span class="sxs-lookup"><span data-stu-id="1d20f-331">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="1d20f-332">以下 `PasswordField` 组件（*self.passwordfield.text*）：</span><span class="sxs-lookup"><span data-stu-id="1d20f-332">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="1d20f-333">将 `<input>` 元素的值设置为 `Password` 属性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-333">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="1d20f-334">使用[EventCallback](#eventcallback)向父组件公开 `Password` 属性的更改。</span><span class="sxs-lookup"><span data-stu-id="1d20f-334">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="1d20f-335">`PasswordField` 组件用于另一个组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-335">The `PasswordField` component is used in another component:</span></span>

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="1d20f-336">对上述示例中的密码执行检查或陷阱错误：</span><span class="sxs-lookup"><span data-stu-id="1d20f-336">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="1d20f-337">为 `Password` 创建支持字段（在下面的示例代码中 `password`）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-337">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="1d20f-338">执行 `Password` setter 中的检查或陷阱错误。</span><span class="sxs-lookup"><span data-stu-id="1d20f-338">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="1d20f-339">如果密码的值中使用了空格，则以下示例向用户提供即时反馈：</span><span class="sxs-lookup"><span data-stu-id="1d20f-339">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a><span data-ttu-id="1d20f-340">捕获对组件的引用</span><span class="sxs-lookup"><span data-stu-id="1d20f-340">Capture references to components</span></span>

<span data-ttu-id="1d20f-341">组件引用提供了一种方法来引用组件实例，以便可以向该实例发出命令，如 `Show` 或 `Reset`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-341">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="1d20f-342">捕获组件引用：</span><span class="sxs-lookup"><span data-stu-id="1d20f-342">To capture a component reference:</span></span>

* <span data-ttu-id="1d20f-343">向子组件添加[@ref](xref:mvc/views/razor#ref)属性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-343">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="1d20f-344">定义与子组件类型相同的字段。</span><span class="sxs-lookup"><span data-stu-id="1d20f-344">Define a field with the same type as the child component.</span></span>

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="1d20f-345">呈现组件时，将用 `MyLoginDialog` 子组件实例填充 `loginDialog` 字段。</span><span class="sxs-lookup"><span data-stu-id="1d20f-345">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="1d20f-346">然后，可以在组件实例上调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="1d20f-346">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1d20f-347">仅在呈现组件后填充 `loginDialog` 变量，其输出包含 `MyLoginDialog` 元素。</span><span class="sxs-lookup"><span data-stu-id="1d20f-347">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="1d20f-348">在该点之前，没有任何内容可供参考。</span><span class="sxs-lookup"><span data-stu-id="1d20f-348">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="1d20f-349">若要在组件完成呈现后操作组件引用，请使用[OnAfterRenderAsync 或 OnAfterRender 方法](#lifecycle-methods)。</span><span class="sxs-lookup"><span data-stu-id="1d20f-349">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](#lifecycle-methods).</span></span>

<span data-ttu-id="1d20f-350">捕获组件引用时，请使用类似的语法来[捕获元素引用](xref:blazor/javascript-interop#capture-references-to-elements)，而不是[JavaScript 互操作](xref:blazor/javascript-interop)功能。</span><span class="sxs-lookup"><span data-stu-id="1d20f-350">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="1d20f-351">不会将组件引用传递给 JavaScript 代码 &mdash;they 仅用于 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="1d20f-351">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="1d20f-352">不要**使用组件**引用来改变子组件的状态。</span><span class="sxs-lookup"><span data-stu-id="1d20f-352">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="1d20f-353">请改用常规声明性参数将数据传递给子组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-353">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="1d20f-354">使用正常的声明性参数会导致子组件自动诸如此类正确的时间。</span><span class="sxs-lookup"><span data-stu-id="1d20f-354">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="1d20f-355">在外部调用组件方法以更新状态</span><span class="sxs-lookup"><span data-stu-id="1d20f-355">Invoke component methods externally to update state</span></span>

<span data-ttu-id="1d20f-356">Blazor 使用 `SynchronizationContext` 来强制执行单个逻辑线程。</span><span class="sxs-lookup"><span data-stu-id="1d20f-356">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="1d20f-357">此 `SynchronizationContext` 上将执行 Blazor 引发的组件生命周期方法和任何事件回调。</span><span class="sxs-lookup"><span data-stu-id="1d20f-357">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="1d20f-358">如果必须根据外部事件（如计时器或其他通知）更新组件，请使用 `InvokeAsync` 方法，该方法将调度到 Blazor 的 `SynchronizationContext`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-358">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="1d20f-359">例如，假设有一个*通告程序服务*可以通知已更新状态的任何侦听组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-359">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="1d20f-360">使用 `NotifierService` 更新组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-360">Usage of the `NotifierService` to update a component:</span></span>

```cshtml
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

<span data-ttu-id="1d20f-361">在前面的示例中，`NotifierService` 在 Blazor 的 `SynchronizationContext` 之外调用组件的 `OnNotify` 方法。</span><span class="sxs-lookup"><span data-stu-id="1d20f-361">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="1d20f-362">`InvokeAsync` 用于切换到正确的上下文，并将呈现器排队。</span><span class="sxs-lookup"><span data-stu-id="1d20f-362">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="1d20f-363">使用 \@key 来控制是否保留元素和组件</span><span class="sxs-lookup"><span data-stu-id="1d20f-363">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="1d20f-364">在呈现元素或组件的列表并且元素或组件随后发生变化时，Blazor 的比较算法必须决定哪些之前的元素或组件可以保留，以及模型对象应如何映射到它们。</span><span class="sxs-lookup"><span data-stu-id="1d20f-364">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="1d20f-365">通常，此过程是自动的，可以忽略，但在某些情况下，您可能需要控制该过程。</span><span class="sxs-lookup"><span data-stu-id="1d20f-365">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="1d20f-366">请看下面的示例：</span><span class="sxs-lookup"><span data-stu-id="1d20f-366">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="1d20f-367">`People` 集合的内容可能会随插入、删除或重新排序条目而更改。</span><span class="sxs-lookup"><span data-stu-id="1d20f-367">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="1d20f-368">当组件 rerenders 时，`<DetailsEditor>` 组件可能会更改以接收不同 `Details` 参数值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-368">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="1d20f-369">这可能导致比预期更复杂的 rerendering。</span><span class="sxs-lookup"><span data-stu-id="1d20f-369">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="1d20f-370">在某些情况下，rerendering 可能会导致可见行为差异，如失去元素焦点。</span><span class="sxs-lookup"><span data-stu-id="1d20f-370">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="1d20f-371">可以用 `@key` 指令特性来控制映射过程。</span><span class="sxs-lookup"><span data-stu-id="1d20f-371">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="1d20f-372">`@key` 会使比较算法根据键的值保证保留元素或组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-372">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="1d20f-373">当 `People` 集合发生更改时，比较算法会保留 `<DetailsEditor>` 实例与 `person` 实例之间的关联：</span><span class="sxs-lookup"><span data-stu-id="1d20f-373">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="1d20f-374">如果从 `People` 列表中删除 `Person`，则仅从 UI 中删除相应的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="1d20f-374">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="1d20f-375">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="1d20f-375">Other instances are left unchanged.</span></span>
* <span data-ttu-id="1d20f-376">如果在列表中的某个位置插入 `Person`，则会在相应位置插入一个新的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="1d20f-376">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="1d20f-377">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="1d20f-377">Other instances are left unchanged.</span></span>
* <span data-ttu-id="1d20f-378">如果重新排序 `Person` 项，则将保留相应的 `<DetailsEditor>` 实例，并在 UI 中重新排序。</span><span class="sxs-lookup"><span data-stu-id="1d20f-378">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="1d20f-379">在某些情况下，使用 `@key` 可最大程度地降低 rerendering 的复杂性，并避免在 DOM 的有状态部分发生变化时出现潜在问题，如焦点位置。</span><span class="sxs-lookup"><span data-stu-id="1d20f-379">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1d20f-380">键对于每个容器元素或组件都是本地的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-380">Keys are local to each container element or component.</span></span> <span data-ttu-id="1d20f-381">不会在整个文档中全局地比较键。</span><span class="sxs-lookup"><span data-stu-id="1d20f-381">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="1d20f-382">何时使用 \@key</span><span class="sxs-lookup"><span data-stu-id="1d20f-382">When to use \@key</span></span>

<span data-ttu-id="1d20f-383">通常，每当呈现列表时（例如，在 `@foreach` 块中）和适当的值（用于定义 `@key`），都有必要使用 `@key`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-383">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="1d20f-384">当对象发生更改时，还可以使用 `@key` 来防止 Blazor 保留元素或组件子树：</span><span class="sxs-lookup"><span data-stu-id="1d20f-384">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="1d20f-385">如果 `@currentPerson` 更改，则 `@key` attribute 指令强制 Blazor 丢弃整个 `<div>` 及其后代，并将 UI 中的子树与新元素和组件重新生成。</span><span class="sxs-lookup"><span data-stu-id="1d20f-385">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="1d20f-386">如果需要确保在 `@currentPerson` 更改时不保留 UI 状态，这会很有用。</span><span class="sxs-lookup"><span data-stu-id="1d20f-386">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="1d20f-387">何时不使用 \@key</span><span class="sxs-lookup"><span data-stu-id="1d20f-387">When not to use \@key</span></span>

<span data-ttu-id="1d20f-388">与 `@key` 进行比较时，性能会降低。</span><span class="sxs-lookup"><span data-stu-id="1d20f-388">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="1d20f-389">性能开销不大，但只有在控制元素或组件保留规则对应用有利的情况下才指定 `@key`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-389">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="1d20f-390">即使不使用 `@key`，Blazor 也会尽可能地保留子元素和组件实例。</span><span class="sxs-lookup"><span data-stu-id="1d20f-390">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="1d20f-391">使用 `@key` 的唯一优点是控制*如何*将模型实例映射到保留的组件实例，而不是选择映射的比较算法。</span><span class="sxs-lookup"><span data-stu-id="1d20f-391">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="1d20f-392">要用于 \@key 的值</span><span class="sxs-lookup"><span data-stu-id="1d20f-392">What values to use for \@key</span></span>

<span data-ttu-id="1d20f-393">通常，为 `@key` 提供以下类型之一：</span><span class="sxs-lookup"><span data-stu-id="1d20f-393">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="1d20f-394">模型对象实例（例如，在前面的示例中为 `Person` 实例）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-394">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="1d20f-395">这可确保基于对象引用相等性保存。</span><span class="sxs-lookup"><span data-stu-id="1d20f-395">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="1d20f-396">唯一标识符（例如，类型的主键值 `int`、`string` 或 `Guid`）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-396">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="1d20f-397">确保用于 `@key` 的值不冲突。</span><span class="sxs-lookup"><span data-stu-id="1d20f-397">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="1d20f-398">如果在同一父元素内检测到冲突值，则 Blazor 会引发异常，因为它无法确定将旧元素或组件映射到新元素或组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-398">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="1d20f-399">仅使用非重复值，例如对象实例或主键值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-399">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="1d20f-400">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="1d20f-400">Lifecycle methods</span></span>

<span data-ttu-id="1d20f-401">`OnInitializedAsync` 和 `OnInitialized` 执行代码来初始化组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-401">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="1d20f-402">若要执行异步操作，请使用 `OnInitializedAsync` 和操作的 `await` 关键字：</span><span class="sxs-lookup"><span data-stu-id="1d20f-402">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="1d20f-403">在 `OnInitializedAsync` 生命周期事件期间，必须在组件初始化期间进行异步工作。</span><span class="sxs-lookup"><span data-stu-id="1d20f-403">Asynchronous work during component initialization must occur during the `OnInitializedAsync` lifecycle event.</span></span>

<span data-ttu-id="1d20f-404">对于同步操作，请使用 `OnInitialized`：</span><span class="sxs-lookup"><span data-stu-id="1d20f-404">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="1d20f-405">当组件已从其父级接收参数并且为属性分配了值时，将调用 `OnParametersSetAsync` 和 `OnParametersSet`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-405">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="1d20f-406">这些方法在组件初始化之后以及每次呈现组件时执行：</span><span class="sxs-lookup"><span data-stu-id="1d20f-406">These methods are executed after component initialization and each time the component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="1d20f-407">应用参数和属性值时的异步工作必须在 `OnParametersSetAsync` 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="1d20f-407">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="1d20f-408">`OnAfterRenderAsync` 和 `OnAfterRender` 在组件完成呈现后调用。</span><span class="sxs-lookup"><span data-stu-id="1d20f-408">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="1d20f-409">此时将填充元素和组件引用。</span><span class="sxs-lookup"><span data-stu-id="1d20f-409">Element and component references are populated at this point.</span></span> <span data-ttu-id="1d20f-410">使用此阶段，可以使用呈现的内容来执行其他初始化步骤，如激活在呈现的 DOM 元素上操作的第三方 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="1d20f-410">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="1d20f-411">在*服务器上预呈现时不会调用*`OnAfterRender`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-411">`OnAfterRender` *isn't called when prerendering on the server.*</span></span>

<span data-ttu-id="1d20f-412">`OnAfterRenderAsync` 的 `firstRender` 参数和 `OnAfterRender` 为：</span><span class="sxs-lookup"><span data-stu-id="1d20f-412">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="1d20f-413">第一次调用组件实例时，设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-413">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="1d20f-414">确保仅执行一次初始化工作。</span><span class="sxs-lookup"><span data-stu-id="1d20f-414">Ensures that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="1d20f-415">呈现后，应立即在 `OnAfterRenderAsync` 生命周期事件期间进行异步工作。</span><span class="sxs-lookup"><span data-stu-id="1d20f-415">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="1d20f-416">在呈现时处理未完成的异步操作</span><span class="sxs-lookup"><span data-stu-id="1d20f-416">Handle incomplete async actions at render</span></span>

<span data-ttu-id="1d20f-417">在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="1d20f-417">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="1d20f-418">在执行生命周期方法时，对象可能 `null` 或未完全填充数据。</span><span class="sxs-lookup"><span data-stu-id="1d20f-418">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="1d20f-419">提供呈现逻辑以确认对象已初始化。</span><span class="sxs-lookup"><span data-stu-id="1d20f-419">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="1d20f-420">`null`对象时呈现占位符 UI 元素（例如，加载消息）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-420">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="1d20f-421">在 Blazor 模板的 `FetchData` 组件中，`OnInitializedAsync` 被重写为 asychronously 接收预测数据（`forecasts`）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-421">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="1d20f-422">`null``forecasts` 时，将向用户显示一条加载消息。</span><span class="sxs-lookup"><span data-stu-id="1d20f-422">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="1d20f-423">`OnInitializedAsync` 的 `Task` 返回完成后，组件将重新呈现已更新状态。</span><span class="sxs-lookup"><span data-stu-id="1d20f-423">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="1d20f-424">*Pages/FetchData.razor*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-424">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="1d20f-425">在设置参数之前执行代码</span><span class="sxs-lookup"><span data-stu-id="1d20f-425">Execute code before parameters are set</span></span>

<span data-ttu-id="1d20f-426">在设置参数之前，可以重写 `SetParameters` 以执行代码：</span><span class="sxs-lookup"><span data-stu-id="1d20f-426">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="1d20f-427">如果未调用 `base.SetParameters`，则自定义代码可以通过任何所需的方式解释传入参数值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-427">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="1d20f-428">例如，无需将传入参数分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-428">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="1d20f-429">禁止刷新 UI</span><span class="sxs-lookup"><span data-stu-id="1d20f-429">Suppress refreshing of the UI</span></span>

<span data-ttu-id="1d20f-430">可以重写 `ShouldRender` 以禁止刷新 UI。</span><span class="sxs-lookup"><span data-stu-id="1d20f-430">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="1d20f-431">如果实现返回 `true`，则刷新 UI。</span><span class="sxs-lookup"><span data-stu-id="1d20f-431">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="1d20f-432">即使重写 `ShouldRender`，组件也始终最初呈现。</span><span class="sxs-lookup"><span data-stu-id="1d20f-432">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="1d20f-433">用 IDisposable 进行的组件处置</span><span class="sxs-lookup"><span data-stu-id="1d20f-433">Component disposal with IDisposable</span></span>

<span data-ttu-id="1d20f-434">如果某个组件实现 <xref:System.IDisposable>，则当从 UI 中删除该组件时，将调用[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="1d20f-434">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="1d20f-435">以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：</span><span class="sxs-lookup"><span data-stu-id="1d20f-435">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="1d20f-436">不支持在 `Dispose` 中调用 `StateHasChanged`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-436">Calling `StateHasChanged` in `Dispose` isn't supported.</span></span> <span data-ttu-id="1d20f-437">`StateHasChanged` 可能会在要关闭的呈现器中被调用。</span><span class="sxs-lookup"><span data-stu-id="1d20f-437">`StateHasChanged` might be invoked as part of the renderer being torn down.</span></span> <span data-ttu-id="1d20f-438">不支持在该点请求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="1d20f-438">Requesting UI updates at that point isn't supported.</span></span>

## <a name="routing"></a><span data-ttu-id="1d20f-439">路由</span><span class="sxs-lookup"><span data-stu-id="1d20f-439">Routing</span></span>

<span data-ttu-id="1d20f-440">通过向应用程序中的每个可访问组件提供路由模板，实现 Blazor 中的路由。</span><span class="sxs-lookup"><span data-stu-id="1d20f-440">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="1d20f-441">编译具有 `@page` 指令的 Razor 文件时，将为生成的类指定 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 指定路由模板的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-441">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="1d20f-442">在运行时，路由器将使用 `RouteAttribute` 查找组件类，并呈现哪个组件包含与请求的 URL 匹配的路由模板。</span><span class="sxs-lookup"><span data-stu-id="1d20f-442">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="1d20f-443">可以将多个路由模板应用于组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-443">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="1d20f-444">以下组件响应 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求：</span><span class="sxs-lookup"><span data-stu-id="1d20f-444">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="1d20f-445">路由参数</span><span class="sxs-lookup"><span data-stu-id="1d20f-445">Route parameters</span></span>

<span data-ttu-id="1d20f-446">组件可以从 `@page` 指令提供的路由模板接收路由参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-446">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="1d20f-447">路由器使用路由参数来填充相应的组件参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-447">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="1d20f-448">*路由参数组件*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-448">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="1d20f-449">不支持可选参数，因此在上面的示例中应用了两个 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="1d20f-449">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="1d20f-450">第一个允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-450">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="1d20f-451">第二个 `@page` 指令采用 `{text}` 路由参数，并将该值分配给 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-451">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="partial-class-support"></a><span data-ttu-id="1d20f-452">分部类支持</span><span class="sxs-lookup"><span data-stu-id="1d20f-452">Partial class support</span></span>

<span data-ttu-id="1d20f-453">Razor 组件以分部类的形式生成。</span><span class="sxs-lookup"><span data-stu-id="1d20f-453">Razor components are generated as partial classes.</span></span> <span data-ttu-id="1d20f-454">使用以下方法之一创作 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-454">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="1d20f-455">C#在一个文件中使用 HTML 标记和 Razor 代码在[@code](xref:mvc/views/razor#code)块中定义代码。</span><span class="sxs-lookup"><span data-stu-id="1d20f-455">C# code is defined in an [@code](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="1d20f-456">Blazor 模板使用此方法来定义其 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-456">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="1d20f-457">C#代码位于定义为分部类的代码隐藏文件中。</span><span class="sxs-lookup"><span data-stu-id="1d20f-457">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="1d20f-458">下面的示例演示了在 Blazor 模板生成的应用中具有 `@code` 块的默认 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-458">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="1d20f-459">HTML 标记、Razor 代码和C#代码位于同一个文件中：</span><span class="sxs-lookup"><span data-stu-id="1d20f-459">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="1d20f-460">*Counter*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-460">*Counter.razor*:</span></span>

```cshtml
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="1d20f-461">还可以使用具有分部类的代码隐藏文件创建 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-461">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="1d20f-462">*Counter*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-462">*Counter.razor*:</span></span>

```cshtml
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="1d20f-463">*Counter.razor.cs*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-463">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

## <a name="specify-a-component-base-class"></a><span data-ttu-id="1d20f-464">指定组件基类</span><span class="sxs-lookup"><span data-stu-id="1d20f-464">Specify a component base class</span></span>

<span data-ttu-id="1d20f-465">`@inherits` 指令可用于指定组件的基类。</span><span class="sxs-lookup"><span data-stu-id="1d20f-465">The `@inherits` directive can be used to specify a base class for a component.</span></span>

<span data-ttu-id="1d20f-466">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)演示组件如何继承基类 `BlazorRocksBase`，以便提供组件的属性和方法。</span><span class="sxs-lookup"><span data-stu-id="1d20f-466">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="1d20f-467">*Pages/BlazorRocks*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-467">*Pages/BlazorRocks.razor*:</span></span>

```cshtml
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="1d20f-468">*BlazorRocksBase.cs*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-468">*BlazorRocksBase.cs*:</span></span>

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

<span data-ttu-id="1d20f-469">基类应派生自 `ComponentBase`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-469">The base class should derive from `ComponentBase`.</span></span>

::: moniker-end

## <a name="import-components"></a><span data-ttu-id="1d20f-470">导入组件</span><span class="sxs-lookup"><span data-stu-id="1d20f-470">Import components</span></span>

<span data-ttu-id="1d20f-471">使用 Razor 编写的组件的命名空间基于（按优先级顺序）：</span><span class="sxs-lookup"><span data-stu-id="1d20f-471">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="1d20f-472">Razor 文件（*razor*）标记中[@namespace](xref:mvc/views/razor#namespace)指定（`@namespace BlazorSample.MyNamespace`）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-472">[@namespace](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="1d20f-473">项目在项目文件中的 `RootNamespace` （`<RootNamespace>BlazorSample</RootNamespace>`）。</span><span class="sxs-lookup"><span data-stu-id="1d20f-473">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="1d20f-474">项目名称，从项目文件的文件名（ *.csproj*）获取，并从项目根路径到组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-474">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="1d20f-475">例如，框架将 *{PROJECT ROOT}/Pages/Index.razor* （*BlazorSample*）解析为命名空间 `BlazorSample.Pages`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-475">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="1d20f-476">组件遵循C#名称绑定规则。</span><span class="sxs-lookup"><span data-stu-id="1d20f-476">Components follow C# name binding rules.</span></span> <span data-ttu-id="1d20f-477">对于本示例中的 `Index` 组件，范围内的组件都是组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-477">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="1d20f-478">在相同的文件夹中，*页*。</span><span class="sxs-lookup"><span data-stu-id="1d20f-478">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="1d20f-479">项目的根中未显式指定其他命名空间的组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-479">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="1d20f-480">使用 Razor 的[@using](xref:mvc/views/razor#using)指令将不同命名空间中定义的组件引入作用域。</span><span class="sxs-lookup"><span data-stu-id="1d20f-480">Components defined in a different namespace are brought into scope using Razor's [@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="1d20f-481">如果*BlazorSample/Shared/* 文件夹中存在另一个组件 `NavMenu.razor`，则可以使用以下 `@using` 语句在 `Index.razor` 中使用该组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-481">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="1d20f-482">还可以使用其完全限定名称（不需要[@using](xref:mvc/views/razor#using)指令）来引用组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-482">Components can also be referenced using their fully qualified names, which doesn't require the [@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="1d20f-483">不支持 `global::` 限制。</span><span class="sxs-lookup"><span data-stu-id="1d20f-483">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="1d20f-484">不支持导入具有化名 `using` 语句（例如，`@using Foo = Bar`）的组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-484">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="1d20f-485">不支持部分限定的名称。</span><span class="sxs-lookup"><span data-stu-id="1d20f-485">Partially qualified names aren't supported.</span></span> <span data-ttu-id="1d20f-486">例如，不支持添加 `@using BlazorSample` 和引用 `<Shared.NavMenu></Shared.NavMenu>` `NavMenu.razor`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-486">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="1d20f-487">条件 HTML 元素特性</span><span class="sxs-lookup"><span data-stu-id="1d20f-487">Conditional HTML element attributes</span></span>

<span data-ttu-id="1d20f-488">HTML 元素特性根据 .NET 值有条件地呈现。</span><span class="sxs-lookup"><span data-stu-id="1d20f-488">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="1d20f-489">如果该值 `false` 或 `null`，则不会呈现特性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-489">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="1d20f-490">如果值为 `true`，则以最小化的特性呈现特性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-490">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="1d20f-491">在下面的示例中，`IsCompleted` 确定元素的标记中是否呈现 `checked`：</span><span class="sxs-lookup"><span data-stu-id="1d20f-491">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="1d20f-492">如果 `true` `IsCompleted`，则复选框将呈现为：</span><span class="sxs-lookup"><span data-stu-id="1d20f-492">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="1d20f-493">如果 `false` `IsCompleted`，则复选框将呈现为：</span><span class="sxs-lookup"><span data-stu-id="1d20f-493">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="1d20f-494">有关更多信息，请参见<xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="1d20f-494">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="1d20f-495">当 .NET 类型为 `bool` 时，某些 HTML 特性（如[aria 按下](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)）不会正常运行。</span><span class="sxs-lookup"><span data-stu-id="1d20f-495">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="1d20f-496">在这些情况下，请使用 `string` 类型，而不是 `bool`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-496">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="1d20f-497">原始 HTML</span><span class="sxs-lookup"><span data-stu-id="1d20f-497">Raw HTML</span></span>

<span data-ttu-id="1d20f-498">通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文本文本。</span><span class="sxs-lookup"><span data-stu-id="1d20f-498">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="1d20f-499">若要呈现原始 HTML，请将 HTML 内容换 `MarkupString` 值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-499">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="1d20f-500">该值将分析为 HTML 或 SVG，并插入 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="1d20f-500">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="1d20f-501">呈现从任何不受信任的源构造的原始 HTML 会**带来安全风险**，应避免！</span><span class="sxs-lookup"><span data-stu-id="1d20f-501">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="1d20f-502">下面的示例演示如何使用 `MarkupString` 类型向组件的呈现输出添加静态 HTML 内容块：</span><span class="sxs-lookup"><span data-stu-id="1d20f-502">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="1d20f-503">模板化组件</span><span class="sxs-lookup"><span data-stu-id="1d20f-503">Templated components</span></span>

<span data-ttu-id="1d20f-504">模板化组件是接受一个或多个 UI 模板作为参数的组件，可将其用作组件呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="1d20f-504">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="1d20f-505">模板化组件允许你创作比常规组件更易于使用的更高级别的组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-505">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="1d20f-506">几个示例包括：</span><span class="sxs-lookup"><span data-stu-id="1d20f-506">A couple of examples include:</span></span>

* <span data-ttu-id="1d20f-507">允许用户为表的标头、行和脚注指定模板的表组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-507">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="1d20f-508">允许用户在列表中指定用于呈现项的模板的列表组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-508">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="1d20f-509">模板参数</span><span class="sxs-lookup"><span data-stu-id="1d20f-509">Template parameters</span></span>

<span data-ttu-id="1d20f-510">模板化组件是通过指定一个或多个 `RenderFragment` 或 `RenderFragment<T>` 类型的组件参数定义的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-510">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="1d20f-511">呈现片段表示要呈现的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="1d20f-511">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="1d20f-512">`RenderFragment<T>` 采用可在调用呈现片段时指定的类型参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-512">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="1d20f-513">`TableTemplate` 组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-513">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="1d20f-514">使用模板化组件时，可以使用与参数名称匹配的子元素（在以下示例中为 `TableHeader` 和 `RowTemplate`）指定模板参数：</span><span class="sxs-lookup"><span data-stu-id="1d20f-514">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a><span data-ttu-id="1d20f-515">模板上下文参数</span><span class="sxs-lookup"><span data-stu-id="1d20f-515">Template context parameters</span></span>

<span data-ttu-id="1d20f-516">作为元素传递的类型 `RenderFragment<T>` 的组件参数具有一个名为 `context` 的隐式参数（例如，前面的代码示例 `@context.PetId`），但你可以使用子元素上的 `Context` 特性来更改参数名称。</span><span class="sxs-lookup"><span data-stu-id="1d20f-516">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="1d20f-517">在下面的示例中，`RowTemplate` 元素的 `Context` 特性指定了 `pet` 参数：</span><span class="sxs-lookup"><span data-stu-id="1d20f-517">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="1d20f-518">另外，还可以在 component 元素上指定 `Context` 属性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-518">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="1d20f-519">指定的 `Context` 特性适用于所有指定的模板参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-519">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="1d20f-520">如果要为隐式子内容指定内容参数名称（不包含任何换行子元素），这会很有用。</span><span class="sxs-lookup"><span data-stu-id="1d20f-520">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="1d20f-521">在下面的示例中，`Context` 特性显示在 `TableTemplate` 元素上，并应用于所有模板参数：</span><span class="sxs-lookup"><span data-stu-id="1d20f-521">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a><span data-ttu-id="1d20f-522">泛型类型化组件</span><span class="sxs-lookup"><span data-stu-id="1d20f-522">Generic-typed components</span></span>

<span data-ttu-id="1d20f-523">模板化组件通常是通用类型。</span><span class="sxs-lookup"><span data-stu-id="1d20f-523">Templated components are often generically typed.</span></span> <span data-ttu-id="1d20f-524">例如，泛型 `ListViewTemplate` 组件可用于呈现 `IEnumerable<T>` 值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-524">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="1d20f-525">若要定义一般组件，请使用[@typeparam](xref:mvc/views/razor#typeparam)指令指定类型参数：</span><span class="sxs-lookup"><span data-stu-id="1d20f-525">To define a generic component, use the [@typeparam](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="1d20f-526">当使用泛型类型的组件时，将在可能的情况下推断类型参数：</span><span class="sxs-lookup"><span data-stu-id="1d20f-526">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="1d20f-527">否则，必须使用与类型参数的名称匹配的属性显式指定 type 参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-527">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="1d20f-528">在下面的示例中，`TItem="Pet"` 指定类型：</span><span class="sxs-lookup"><span data-stu-id="1d20f-528">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="1d20f-529">级联值和参数</span><span class="sxs-lookup"><span data-stu-id="1d20f-529">Cascading values and parameters</span></span>

<span data-ttu-id="1d20f-530">在某些情况下，使用[组件参数](#component-parameters)将数据从祖先组件流式传输到附属组件是不方便的，尤其是在有多个组件层时。</span><span class="sxs-lookup"><span data-stu-id="1d20f-530">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="1d20f-531">级联值和参数通过提供一种方便的方法，使上级组件为其所有子代组件提供值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-531">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="1d20f-532">级联值和参数还提供了一种方法来协调组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-532">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="1d20f-533">主题示例</span><span class="sxs-lookup"><span data-stu-id="1d20f-533">Theme example</span></span>

<span data-ttu-id="1d20f-534">在下面的示例应用程序示例中，`ThemeInfo` 类指定用于向下传递组件层次结构的主题信息，以便应用程序中给定部分内的所有按钮共享相同样式。</span><span class="sxs-lookup"><span data-stu-id="1d20f-534">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="1d20f-535">*UIThemeClasses/ThemeInfo*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-535">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="1d20f-536">祖先组件可以使用级联值组件提供级联值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-536">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="1d20f-537">`CascadingValue` 组件包装组件层次结构的子树，并向该子树内的所有组件提供单个值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-537">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="1d20f-538">例如，示例应用程序将应用程序布局之一中的主题信息（`ThemeInfo`）指定为构成 `@Body` 属性布局正文的所有组件的级联参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-538">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="1d20f-539">在布局组件中为 `ButtonClass` 分配 `btn-success` 值。</span><span class="sxs-lookup"><span data-stu-id="1d20f-539">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="1d20f-540">任何子代组件都可以通过 `ThemeInfo` 级联对象使用此属性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-540">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="1d20f-541">`CascadingValuesParametersLayout` 组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-541">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="1d20f-542">为了使用级联值，组件使用 `[CascadingParameter]` 属性声明级联参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-542">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="1d20f-543">级联值按类型绑定到级联参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-543">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="1d20f-544">在示例应用中，`CascadingValuesParametersTheme` 组件将 `ThemeInfo` 级联值绑定到级联参数。</span><span class="sxs-lookup"><span data-stu-id="1d20f-544">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="1d20f-545">参数用于设置由组件显示的一个按钮的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="1d20f-545">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="1d20f-546">`CascadingValuesParametersTheme` 组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-546">`CascadingValuesParametersTheme` component:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="1d20f-547">若要在同一子树内级联多个相同类型的值，请为每个 `CascadingValue` 组件及其相应的 `CascadingParameter` 提供唯一的 `Name` 字符串。</span><span class="sxs-lookup"><span data-stu-id="1d20f-547">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="1d20f-548">在下面的示例中，两个 `CascadingValue` 组件按名称级联了 `MyCascadingType` 的不同实例：</span><span class="sxs-lookup"><span data-stu-id="1d20f-548">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="1d20f-549">在子代组件中，级联参数按名称从上级组件中相应的级联值接收值：</span><span class="sxs-lookup"><span data-stu-id="1d20f-549">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="1d20f-550">TabSet 示例</span><span class="sxs-lookup"><span data-stu-id="1d20f-550">TabSet example</span></span>

<span data-ttu-id="1d20f-551">级联参数还允许组件跨组件层次结构进行协作。</span><span class="sxs-lookup"><span data-stu-id="1d20f-551">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="1d20f-552">例如，请看下面的示例应用中的*TabSet*示例。</span><span class="sxs-lookup"><span data-stu-id="1d20f-552">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="1d20f-553">该示例应用包含一个选项卡实现的 `ITab` 接口：</span><span class="sxs-lookup"><span data-stu-id="1d20f-553">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="1d20f-554">`CascadingValuesParametersTabSet` 组件使用 `TabSet` 组件，该组件包含多个 `Tab` 组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-554">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="1d20f-555">子 `Tab` 组件不会显式作为参数传递给 `TabSet`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-555">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="1d20f-556">子 `Tab` 组件是 `TabSet` 的子内容的一部分。</span><span class="sxs-lookup"><span data-stu-id="1d20f-556">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="1d20f-557">但 `TabSet` 仍需要了解每个 `Tab` 组件，以便它可以呈现标头和活动选项卡。若要启用此协调而不需要额外的代码，`TabSet` 组件*可将自身作为级联值提供*，然后由子代 `Tab` 组件选取。</span><span class="sxs-lookup"><span data-stu-id="1d20f-557">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="1d20f-558">`TabSet` 组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-558">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="1d20f-559">子代 `Tab` 组件将包含 `TabSet` 作为级联参数捕获，因此 `Tab` 组件会将其自身添加到 `TabSet` 并协调哪个选项卡处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="1d20f-559">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="1d20f-560">`Tab` 组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-560">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="1d20f-561">Razor 模板</span><span class="sxs-lookup"><span data-stu-id="1d20f-561">Razor templates</span></span>

<span data-ttu-id="1d20f-562">可以使用 Razor 模板语法来定义呈现片段。</span><span class="sxs-lookup"><span data-stu-id="1d20f-562">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="1d20f-563">Razor 模板是一种定义 UI 代码段并采用以下格式的一种方法：</span><span class="sxs-lookup"><span data-stu-id="1d20f-563">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="1d20f-564">下面的示例演示如何在组件中直接指定 `RenderFragment` 和 `RenderFragment<T>` 值以及呈现模板。</span><span class="sxs-lookup"><span data-stu-id="1d20f-564">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="1d20f-565">呈现片段还可以作为参数传递给[模板化组件](#templated-components)。</span><span class="sxs-lookup"><span data-stu-id="1d20f-565">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="1d20f-566">前面的代码的呈现输出：</span><span class="sxs-lookup"><span data-stu-id="1d20f-566">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="1d20f-567">手动 RenderTreeBuilder 逻辑</span><span class="sxs-lookup"><span data-stu-id="1d20f-567">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="1d20f-568">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` 提供了用于操作组件和元素的方法，包括在代码C#中手动生成组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-568">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="1d20f-569">使用 `RenderTreeBuilder` 创建组件是一种高级方案。</span><span class="sxs-lookup"><span data-stu-id="1d20f-569">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="1d20f-570">格式不正确的组件（例如，未闭合的标记标记）可能会导致未定义的行为。</span><span class="sxs-lookup"><span data-stu-id="1d20f-570">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="1d20f-571">请考虑以下 `PetDetails` 组件，可以手动将其内置到另一个组件中：</span><span class="sxs-lookup"><span data-stu-id="1d20f-571">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="1d20f-572">在下面的示例中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-572">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="1d20f-573">当调用 `RenderTreeBuilder` 方法来创建组件（`OpenComponent` 和 `AddAttribute`）时，序列号为源代码行号。</span><span class="sxs-lookup"><span data-stu-id="1d20f-573">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="1d20f-574">Blazor 差异算法依赖于与不同代码行对应的序列号，而不是不同的调用调用。</span><span class="sxs-lookup"><span data-stu-id="1d20f-574">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="1d20f-575">使用 `RenderTreeBuilder` 方法创建组件时，将序列号的参数硬编码。</span><span class="sxs-lookup"><span data-stu-id="1d20f-575">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="1d20f-576">**使用计算或计数器生成序列号会导致性能不佳。**</span><span class="sxs-lookup"><span data-stu-id="1d20f-576">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="1d20f-577">有关详细信息，请参阅 "[序列号与代码行号和非执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)" 部分。</span><span class="sxs-lookup"><span data-stu-id="1d20f-577">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="1d20f-578">`BuiltContent` 组件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-578">`BuiltContent` component:</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> <span data-ttu-id="1d20f-579">!出现`Microsoft.AspNetCore.Components.RenderTree` 中的类型允许处理呈现操作的*结果*。</span><span class="sxs-lookup"><span data-stu-id="1d20f-579">![WARNING] The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="1d20f-580">这是 Blazor 框架实现的内部详细信息。</span><span class="sxs-lookup"><span data-stu-id="1d20f-580">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="1d20f-581">这些类型应被视为不*稳定*，并且在将来的版本中可能会更改。</span><span class="sxs-lookup"><span data-stu-id="1d20f-581">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="1d20f-582">序列号与代码行号相关，而不是与执行顺序相关</span><span class="sxs-lookup"><span data-stu-id="1d20f-582">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="1d20f-583">Blazor `.razor` 文件始终被编译。</span><span class="sxs-lookup"><span data-stu-id="1d20f-583">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="1d20f-584">这可能是 `.razor` 的优点，因为编译步骤可用于注入在运行时提高应用性能的信息。</span><span class="sxs-lookup"><span data-stu-id="1d20f-584">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="1d20f-585">这些改进涉及到*序列号*的主要示例。</span><span class="sxs-lookup"><span data-stu-id="1d20f-585">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="1d20f-586">序列号指示运行时输出来自代码的不同和有序行。</span><span class="sxs-lookup"><span data-stu-id="1d20f-586">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="1d20f-587">运行时使用此信息以线性时间生成有效的树差异，其速度远快于一般树差异算法通常可以实现的速度。</span><span class="sxs-lookup"><span data-stu-id="1d20f-587">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="1d20f-588">请考虑以下简单的 `.razor` 文件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-588">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="1d20f-589">前面的代码编译为类似于下面的内容：</span><span class="sxs-lookup"><span data-stu-id="1d20f-589">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="1d20f-590">当第一次执行代码时，如果 `true` `someFlag`，生成器将接收：</span><span class="sxs-lookup"><span data-stu-id="1d20f-590">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="1d20f-591">序列</span><span class="sxs-lookup"><span data-stu-id="1d20f-591">Sequence</span></span> | <span data-ttu-id="1d20f-592">键入</span><span class="sxs-lookup"><span data-stu-id="1d20f-592">Type</span></span>      | <span data-ttu-id="1d20f-593">数据</span><span class="sxs-lookup"><span data-stu-id="1d20f-593">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1d20f-594">0</span><span class="sxs-lookup"><span data-stu-id="1d20f-594">0</span></span>        | <span data-ttu-id="1d20f-595">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1d20f-595">Text node</span></span> | <span data-ttu-id="1d20f-596">First</span><span class="sxs-lookup"><span data-stu-id="1d20f-596">First</span></span>  |
| <span data-ttu-id="1d20f-597">1</span><span class="sxs-lookup"><span data-stu-id="1d20f-597">1</span></span>        | <span data-ttu-id="1d20f-598">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1d20f-598">Text node</span></span> | <span data-ttu-id="1d20f-599">秒</span><span class="sxs-lookup"><span data-stu-id="1d20f-599">Second</span></span> |

<span data-ttu-id="1d20f-600">假设 `someFlag` 成为 `false`，并再次呈现标记。</span><span class="sxs-lookup"><span data-stu-id="1d20f-600">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="1d20f-601">这次，生成器将接收：</span><span class="sxs-lookup"><span data-stu-id="1d20f-601">This time, the builder receives:</span></span>

| <span data-ttu-id="1d20f-602">序列</span><span class="sxs-lookup"><span data-stu-id="1d20f-602">Sequence</span></span> | <span data-ttu-id="1d20f-603">键入</span><span class="sxs-lookup"><span data-stu-id="1d20f-603">Type</span></span>       | <span data-ttu-id="1d20f-604">数据</span><span class="sxs-lookup"><span data-stu-id="1d20f-604">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="1d20f-605">1</span><span class="sxs-lookup"><span data-stu-id="1d20f-605">1</span></span>        | <span data-ttu-id="1d20f-606">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1d20f-606">Text node</span></span>  | <span data-ttu-id="1d20f-607">秒</span><span class="sxs-lookup"><span data-stu-id="1d20f-607">Second</span></span> |

<span data-ttu-id="1d20f-608">当运行时执行差异时，它会发现序列 `0` 的项已被删除，因此它生成以下简单的*编辑脚本*：</span><span class="sxs-lookup"><span data-stu-id="1d20f-608">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="1d20f-609">删除第一个文本节点。</span><span class="sxs-lookup"><span data-stu-id="1d20f-609">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="1d20f-610">如果以编程方式生成序列号，会出现什么错误</span><span class="sxs-lookup"><span data-stu-id="1d20f-610">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="1d20f-611">假设你编写了以下呈现树生成器逻辑：</span><span class="sxs-lookup"><span data-stu-id="1d20f-611">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="1d20f-612">现在，第一个输出是：</span><span class="sxs-lookup"><span data-stu-id="1d20f-612">Now, the first output is:</span></span>

| <span data-ttu-id="1d20f-613">序列</span><span class="sxs-lookup"><span data-stu-id="1d20f-613">Sequence</span></span> | <span data-ttu-id="1d20f-614">键入</span><span class="sxs-lookup"><span data-stu-id="1d20f-614">Type</span></span>      | <span data-ttu-id="1d20f-615">数据</span><span class="sxs-lookup"><span data-stu-id="1d20f-615">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1d20f-616">0</span><span class="sxs-lookup"><span data-stu-id="1d20f-616">0</span></span>        | <span data-ttu-id="1d20f-617">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1d20f-617">Text node</span></span> | <span data-ttu-id="1d20f-618">First</span><span class="sxs-lookup"><span data-stu-id="1d20f-618">First</span></span>  |
| <span data-ttu-id="1d20f-619">1</span><span class="sxs-lookup"><span data-stu-id="1d20f-619">1</span></span>        | <span data-ttu-id="1d20f-620">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1d20f-620">Text node</span></span> | <span data-ttu-id="1d20f-621">秒</span><span class="sxs-lookup"><span data-stu-id="1d20f-621">Second</span></span> |

<span data-ttu-id="1d20f-622">此结果与以前的情况相同，因此不存在负面问题。</span><span class="sxs-lookup"><span data-stu-id="1d20f-622">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="1d20f-623">第二个呈现 `false` `someFlag`，输出为：</span><span class="sxs-lookup"><span data-stu-id="1d20f-623">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="1d20f-624">序列</span><span class="sxs-lookup"><span data-stu-id="1d20f-624">Sequence</span></span> | <span data-ttu-id="1d20f-625">键入</span><span class="sxs-lookup"><span data-stu-id="1d20f-625">Type</span></span>      | <span data-ttu-id="1d20f-626">数据</span><span class="sxs-lookup"><span data-stu-id="1d20f-626">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="1d20f-627">0</span><span class="sxs-lookup"><span data-stu-id="1d20f-627">0</span></span>        | <span data-ttu-id="1d20f-628">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1d20f-628">Text node</span></span> | <span data-ttu-id="1d20f-629">秒</span><span class="sxs-lookup"><span data-stu-id="1d20f-629">Second</span></span> |

<span data-ttu-id="1d20f-630">这次，比较算法会发现*两个*更改已发生，并且算法将生成以下编辑脚本：</span><span class="sxs-lookup"><span data-stu-id="1d20f-630">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="1d20f-631">将第一个文本节点的值更改为 `Second`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-631">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="1d20f-632">删除第二个文本节点。</span><span class="sxs-lookup"><span data-stu-id="1d20f-632">Remove the second text node.</span></span>

<span data-ttu-id="1d20f-633">生成序列号丢失了有关原始代码中出现 `if/else` 分支和循环的位置的所有有用信息。</span><span class="sxs-lookup"><span data-stu-id="1d20f-633">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="1d20f-634">这会导致**两次**比较，就像以前一样。</span><span class="sxs-lookup"><span data-stu-id="1d20f-634">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="1d20f-635">这是一个简单的示例。</span><span class="sxs-lookup"><span data-stu-id="1d20f-635">This is a trivial example.</span></span> <span data-ttu-id="1d20f-636">在具有复杂、深度嵌套结构的更真实的情况下，特别是在循环中，性能开销更严重。</span><span class="sxs-lookup"><span data-stu-id="1d20f-636">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="1d20f-637">比较算法不必立即确定哪些循环块或分支已插入或删除，而是必须将其深入地递归到呈现树，并且通常会生成更长的编辑脚本，因为它 misinformed 了新的和新的结构彼此关联。</span><span class="sxs-lookup"><span data-stu-id="1d20f-637">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="1d20f-638">指导和结论</span><span class="sxs-lookup"><span data-stu-id="1d20f-638">Guidance and conclusions</span></span>

* <span data-ttu-id="1d20f-639">如果动态生成了序列号，则应用程序性能会受到影响。</span><span class="sxs-lookup"><span data-stu-id="1d20f-639">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="1d20f-640">框架无法在运行时自动创建自己的序列号，因为所需信息不存在，除非它在编译时捕获。</span><span class="sxs-lookup"><span data-stu-id="1d20f-640">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="1d20f-641">请勿写入长块手动实现 `RenderTreeBuilder` 逻辑。</span><span class="sxs-lookup"><span data-stu-id="1d20f-641">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="1d20f-642">首选 `.razor` 文件，并允许编译器处理序列号。</span><span class="sxs-lookup"><span data-stu-id="1d20f-642">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="1d20f-643">如果无法避免手动 `RenderTreeBuilder` 逻辑，请将长代码块拆分为 `OpenRegion` / `CloseRegion` 调用中的小块。</span><span class="sxs-lookup"><span data-stu-id="1d20f-643">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="1d20f-644">每个区域都有其自己单独的序列号空间，因此可以从每个区域内的零（或任何其他任意数字）重新启动。</span><span class="sxs-lookup"><span data-stu-id="1d20f-644">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="1d20f-645">如果对序列号进行硬编码，则 diff 算法只要求序列号的值增加。</span><span class="sxs-lookup"><span data-stu-id="1d20f-645">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="1d20f-646">初始值和间隙无关。</span><span class="sxs-lookup"><span data-stu-id="1d20f-646">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="1d20f-647">一个合法选项是将代码行号用作序列号，或从零开始，并按一个或数百个（或任何首选间隔）递增。</span><span class="sxs-lookup"><span data-stu-id="1d20f-647">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="1d20f-648">Blazor 使用序列号，而其他树比较的 UI 框架不使用序列号。</span><span class="sxs-lookup"><span data-stu-id="1d20f-648">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="1d20f-649">使用序列号时，比较速度要快得多，Blazor 的优点是编译步骤会自动处理序列号，以便开发人员创作 `.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="1d20f-649">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="1d20f-650">本地化</span><span class="sxs-lookup"><span data-stu-id="1d20f-650">Localization</span></span>

<span data-ttu-id="1d20f-651">使用[本地化中间件](xref:fundamentals/localization#localization-middleware)对 Blazor 服务器应用进行本地化。</span><span class="sxs-lookup"><span data-stu-id="1d20f-651">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="1d20f-652">中间件为从应用程序请求资源的用户选择相应的区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-652">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="1d20f-653">可以使用以下方法之一设置区域性：</span><span class="sxs-lookup"><span data-stu-id="1d20f-653">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="1d20f-654">Cookie</span><span class="sxs-lookup"><span data-stu-id="1d20f-654">Cookies</span></span>](#cookies)
* [<span data-ttu-id="1d20f-655">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="1d20f-655">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="1d20f-656">有关更多信息和示例，请参见<xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="1d20f-656">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="1d20f-657">Cookie</span><span class="sxs-lookup"><span data-stu-id="1d20f-657">Cookies</span></span>

<span data-ttu-id="1d20f-658">本地化区域性 cookie 可以保存用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-658">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="1d20f-659">该 cookie 是由应用程序主机页（*Pages/host. .cs*）的 `OnGet` 方法创建的。</span><span class="sxs-lookup"><span data-stu-id="1d20f-659">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="1d20f-660">本地化中间件会在后续请求上读取 cookie，以设置用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-660">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="1d20f-661">使用 cookie 可确保 WebSocket 连接可以正确地传播区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-661">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="1d20f-662">如果本地化方案基于 URL 路径或查询字符串，则该方案可能无法与 Websocket 一起使用，因此无法持久保存区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-662">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="1d20f-663">因此，建议使用本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="1d20f-663">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="1d20f-664">如果在本地化 cookie 中保留了区域性，则可使用任何方法来分配区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-664">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="1d20f-665">如果应用已建立服务器端 ASP.NET Core 的本地化方案，请继续使用应用的现有本地化基础结构，并在应用方案中设置本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="1d20f-665">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="1d20f-666">下面的示例演示如何在可由本地化中间件读取的 cookie 中设置当前区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-666">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="1d20f-667">在 Blazor 服务器应用中创建包含以下内容的*页面/主机 .cs*文件：</span><span class="sxs-lookup"><span data-stu-id="1d20f-667">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="1d20f-668">在应用程序中处理本地化：</span><span class="sxs-lookup"><span data-stu-id="1d20f-668">Localization is handled in the app:</span></span>

1. <span data-ttu-id="1d20f-669">浏览器将初始 HTTP 请求发送到应用程序。</span><span class="sxs-lookup"><span data-stu-id="1d20f-669">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="1d20f-670">区域性由本地化中间件分配。</span><span class="sxs-lookup"><span data-stu-id="1d20f-670">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="1d20f-671">*_Host*中的 `OnGet` 方法将区域性作为响应的一部分保留在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="1d20f-671">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="1d20f-672">浏览器将打开 WebSocket 连接以创建交互式 Blazor 服务器会话。</span><span class="sxs-lookup"><span data-stu-id="1d20f-672">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="1d20f-673">本地化中间件读取 cookie 并分配区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-673">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="1d20f-674">Blazor 服务器会话以正确的区域性开头。</span><span class="sxs-lookup"><span data-stu-id="1d20f-674">The Blazor Server session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="1d20f-675">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="1d20f-675">Provide UI to choose the culture</span></span>

<span data-ttu-id="1d20f-676">为了提供允许用户选择区域性的 UI，建议使用*基于重定向的方法*。</span><span class="sxs-lookup"><span data-stu-id="1d20f-676">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="1d20f-677">此过程类似于用户尝试访问安全资源时在 web 应用中发生的情况，&mdash;the 用户重定向到登录页，然后重定向回原始资源。</span><span class="sxs-lookup"><span data-stu-id="1d20f-677">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="1d20f-678">应用程序通过重定向到控制器来持久保存用户的所选区域性。</span><span class="sxs-lookup"><span data-stu-id="1d20f-678">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="1d20f-679">控制器将用户选定的区域性设置为 cookie，并将用户重定向回原始 URI。</span><span class="sxs-lookup"><span data-stu-id="1d20f-679">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="1d20f-680">在服务器上创建一个 HTTP 终结点，以在 cookie 中设置用户的所选区域性，并执行重定向回原始 URI：</span><span class="sxs-lookup"><span data-stu-id="1d20f-680">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="1d20f-681">使用 `LocalRedirect` 操作结果可防止开放重定向攻击。</span><span class="sxs-lookup"><span data-stu-id="1d20f-681">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="1d20f-682">有关更多信息，请参见<xref:security/preventing-open-redirects>。</span><span class="sxs-lookup"><span data-stu-id="1d20f-682">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="1d20f-683">以下组件显示了一个示例，说明如何在用户选择区域性时执行初始重定向：</span><span class="sxs-lookup"><span data-stu-id="1d20f-683">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="1d20f-684">在 Blazor 应用中使用 .NET 本地化方案</span><span class="sxs-lookup"><span data-stu-id="1d20f-684">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="1d20f-685">在 Blazor 应用程序中，可以使用以下 .NET 本地化和全球化方案：</span><span class="sxs-lookup"><span data-stu-id="1d20f-685">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="1d20f-686">.网络资源系统</span><span class="sxs-lookup"><span data-stu-id="1d20f-686">.NET's resources system</span></span>
* <span data-ttu-id="1d20f-687">区域性特定的数字和日期格式设置</span><span class="sxs-lookup"><span data-stu-id="1d20f-687">Culture-specific number and date formatting</span></span>

<span data-ttu-id="1d20f-688">Blazor 的 `@bind` 功能基于用户的当前区域性执行全球化。</span><span class="sxs-lookup"><span data-stu-id="1d20f-688">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="1d20f-689">有关详细信息，请参阅[数据绑定](#data-binding)部分。</span><span class="sxs-lookup"><span data-stu-id="1d20f-689">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="1d20f-690">目前支持有限的一组 ASP.NET Core 本地化方案：</span><span class="sxs-lookup"><span data-stu-id="1d20f-690">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="1d20f-691">Blazor 应用*支持*`IStringLocalizer<>`。</span><span class="sxs-lookup"><span data-stu-id="1d20f-691">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="1d20f-692">`IHtmlLocalizer<>`、`IViewLocalizer<>` 和数据批注本地化 ASP.NET Core MVC 方案，在 Blazor 应用中**不受支持**。</span><span class="sxs-lookup"><span data-stu-id="1d20f-692">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="1d20f-693">有关更多信息，请参见<xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="1d20f-693">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="1d20f-694">可缩放的向量图形（SVG）图像</span><span class="sxs-lookup"><span data-stu-id="1d20f-694">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="1d20f-695">由于 Blazor 呈现 HTML，因此支持浏览器支持的图像（包括可缩放的向量图形（SVG）图像（*svg*））是通过 `<img>` 标记支持的：</span><span class="sxs-lookup"><span data-stu-id="1d20f-695">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="1d20f-696">同样，样式表文件（ *.css*）的 CSS 规则支持 SVG 图像：</span><span class="sxs-lookup"><span data-stu-id="1d20f-696">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="1d20f-697">但是，在所有情况下均不支持内联 SVG 标记。</span><span class="sxs-lookup"><span data-stu-id="1d20f-697">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="1d20f-698">如果将 `<svg>` 标记直接放入组件文件（*razor*），则支持基本图像呈现，但尚不支持很多高级方案。</span><span class="sxs-lookup"><span data-stu-id="1d20f-698">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="1d20f-699">例如，当前未遵循 `<use>` 标记，并且不能将 `@bind` 与某些 SVG 标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="1d20f-699">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="1d20f-700">我们预计会在将来的版本中解决这些限制。</span><span class="sxs-lookup"><span data-stu-id="1d20f-700">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d20f-701">其他资源</span><span class="sxs-lookup"><span data-stu-id="1d20f-701">Additional resources</span></span>

* <span data-ttu-id="1d20f-702"><xref:security/blazor/server> &ndash; 包括构建必须与资源耗尽相关的 Blazor 服务器应用的指南。</span><span class="sxs-lookup"><span data-stu-id="1d20f-702"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
