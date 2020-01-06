---
title: 创建和使用 ASP.NET Core Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件，包括如何绑定到数据、处理事件和管理组件生命周期。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/28/2019
no-loc:
- Blazor
uid: blazor/components
ms.openlocfilehash: 87f21d84c17e5bbd1247bb955acee81384b890e7
ms.sourcegitcommit: 47d453f34b6fd0179119c572cb8be64c5365cbb6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75597897"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="1957d-103">创建和使用 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="1957d-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="1957d-104">作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1957d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1957d-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1957d-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1957d-106">使用*组件*构建 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="1957d-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="1957d-107">组件是自包含的用户界面（UI）块，如页、对话框或窗体。</span><span class="sxs-lookup"><span data-stu-id="1957d-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="1957d-108">组件包含插入数据或响应 UI 事件所需的 HTML 标记和处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="1957d-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="1957d-109">组件非常灵活且轻型。</span><span class="sxs-lookup"><span data-stu-id="1957d-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="1957d-110">它们可以嵌套、重复使用和在项目之间共享。</span><span class="sxs-lookup"><span data-stu-id="1957d-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="1957d-111">组件类</span><span class="sxs-lookup"><span data-stu-id="1957d-111">Component classes</span></span>

<span data-ttu-id="1957d-112">在[razor](xref:mvc/views/razor)组件文件（*razor*）中，使用和 HTML 标记的C#组合实现了组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="1957d-113">Blazor 中的组件正式称为*Razor 组件*。</span><span class="sxs-lookup"><span data-stu-id="1957d-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="1957d-114">组件的名称必须以大写字符开头。</span><span class="sxs-lookup"><span data-stu-id="1957d-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="1957d-115">例如， *MyCoolComponent*是有效的，并且*MyCoolComponent*无效。</span><span class="sxs-lookup"><span data-stu-id="1957d-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="1957d-116">使用 HTML 定义组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="1957d-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="1957d-117">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="1957d-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="1957d-118">在编译应用程序时，会将 HTML 标记C#和呈现逻辑转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="1957d-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="1957d-119">生成的类的名称与文件的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="1957d-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="1957d-120">组件类的成员在 `@code` 块中定义。</span><span class="sxs-lookup"><span data-stu-id="1957d-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="1957d-121">在 `@code` 块中，组件状态（属性、字段）是通过用于事件处理的方法或定义其他组件逻辑来指定的。</span><span class="sxs-lookup"><span data-stu-id="1957d-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="1957d-122">允许多个 `@code` 块。</span><span class="sxs-lookup"><span data-stu-id="1957d-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="1957d-123">在 ASP.NET Core 3.0 的先前预览中，`@functions` 块用于与 Razor 组件中 `@code` 块相同的用途。</span><span class="sxs-lookup"><span data-stu-id="1957d-123">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="1957d-124">`@functions` 块继续在 Razor 组件中运行，但我们建议在 ASP.NET Core 3.0 Preview 6 或更高版本中使用 `@code` 块。</span><span class="sxs-lookup"><span data-stu-id="1957d-124">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="1957d-125">可以使用C#以 `@`开头的表达式将组件成员作为组件的呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="1957d-125">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="1957d-126">例如， C#字段通过在字段名称 `@` 前缀来呈现。</span><span class="sxs-lookup"><span data-stu-id="1957d-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="1957d-127">下面的示例计算并呈现：</span><span class="sxs-lookup"><span data-stu-id="1957d-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="1957d-128">`_headingFontStyle` `font-style`的 CSS 属性值。</span><span class="sxs-lookup"><span data-stu-id="1957d-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="1957d-129">`_headingText` 到 `<h1>` 元素的内容。</span><span class="sxs-lookup"><span data-stu-id="1957d-129">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="1957d-130">最初呈现组件后，组件会为响应事件而重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="1957d-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="1957d-131">然后 Blazor 将新的呈现树与上一个树进行比较，并将所有修改应用于浏览器的文档对象模型（DOM）。</span><span class="sxs-lookup"><span data-stu-id="1957d-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="1957d-132">组件是普通C#类，可以放置在项目中的任何位置。</span><span class="sxs-lookup"><span data-stu-id="1957d-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="1957d-133">生成网页的组件通常位于*Pages*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1957d-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="1957d-134">非页组件通常放置在*共享*文件夹中或添加到项目的自定义文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1957d-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="1957d-135">若要使用自定义文件夹，请将自定义文件夹的命名空间添加到父组件或应用程序的 *_Imports*文件。</span><span class="sxs-lookup"><span data-stu-id="1957d-135">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="1957d-136">例如，以下命名空间使*组件*文件夹中的组件在应用的根命名空间 `WebApplication`时可用：</span><span class="sxs-lookup"><span data-stu-id="1957d-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```razor
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="1957d-137">将组件集成到 Razor Pages 和 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="1957d-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="1957d-138">将组件用于现有 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="1957d-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="1957d-139">无需重写现有页面或视图即可使用 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="1957d-140">呈现页面或视图时，组件将同时预呈现。</span><span class="sxs-lookup"><span data-stu-id="1957d-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="1957d-141">若要从页面或视图呈现组件，请使用 `Component` 标记帮助程序：</span><span class="sxs-lookup"><span data-stu-id="1957d-141">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="1957d-142">支持传递参数（例如，在前面的示例中为 `IncrementAmount`）。</span><span class="sxs-lookup"><span data-stu-id="1957d-142">Passing parameters (for example, `IncrementAmount` in the preceding example) is supported.</span></span>

<span data-ttu-id="1957d-143">`RenderMode` 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="1957d-143">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="1957d-144">已预呈现到页面中。</span><span class="sxs-lookup"><span data-stu-id="1957d-144">Is prerendered into the page.</span></span>
* <span data-ttu-id="1957d-145">在页面上呈现为静态 HTML，或者它包含从用户代理启动 Blazor 应用程序所需的信息。</span><span class="sxs-lookup"><span data-stu-id="1957d-145">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="1957d-146">描述</span><span class="sxs-lookup"><span data-stu-id="1957d-146">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="1957d-147">将组件呈现为静态 HTML，并为 Blazor 服务器应用包含标记。</span><span class="sxs-lookup"><span data-stu-id="1957d-147">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="1957d-148">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="1957d-148">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="1957d-149">呈现 Blazor 服务器应用程序的标记。</span><span class="sxs-lookup"><span data-stu-id="1957d-149">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="1957d-150">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="1957d-150">Output from the component isn't included.</span></span> <span data-ttu-id="1957d-151">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="1957d-151">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="1957d-152">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="1957d-152">Renders the component into static HTML.</span></span> |

<span data-ttu-id="1957d-153">尽管页面和视图可以使用组件，但不是这样。</span><span class="sxs-lookup"><span data-stu-id="1957d-153">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="1957d-154">组件不能使用视图和特定于页的方案，如分部视图和节。</span><span class="sxs-lookup"><span data-stu-id="1957d-154">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="1957d-155">若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解为一个组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-155">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="1957d-156">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-156">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="1957d-157">有关如何呈现组件、组件状态和 `Component` 标记帮助器的详细信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="1957d-157">For more information on how components are rendered, component state, and the `Component` Tag Helper, see <xref:blazor/hosting-models>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

<span data-ttu-id="1957d-158">若要从页面或视图呈现组件，请使用 `RenderComponentAsync<TComponent>` HTML 帮助器方法：</span><span class="sxs-lookup"><span data-stu-id="1957d-158">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
@(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
```

<span data-ttu-id="1957d-159">`RenderMode` 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="1957d-159">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="1957d-160">已预呈现到页面中。</span><span class="sxs-lookup"><span data-stu-id="1957d-160">Is prerendered into the page.</span></span>
* <span data-ttu-id="1957d-161">在页面上呈现为静态 HTML，或者它包含从用户代理启动 Blazor 应用程序所需的信息。</span><span class="sxs-lookup"><span data-stu-id="1957d-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="1957d-162">描述</span><span class="sxs-lookup"><span data-stu-id="1957d-162">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="1957d-163">将组件呈现为静态 HTML，并为 Blazor 服务器应用包含标记。</span><span class="sxs-lookup"><span data-stu-id="1957d-163">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="1957d-164">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="1957d-164">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="1957d-165">不支持参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-165">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="1957d-166">呈现 Blazor 服务器应用程序的标记。</span><span class="sxs-lookup"><span data-stu-id="1957d-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="1957d-167">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="1957d-167">Output from the component isn't included.</span></span> <span data-ttu-id="1957d-168">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="1957d-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="1957d-169">不支持参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-169">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="1957d-170">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="1957d-170">Renders the component into static HTML.</span></span> <span data-ttu-id="1957d-171">支持参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-171">Parameters are supported.</span></span> |

<span data-ttu-id="1957d-172">尽管页面和视图可以使用组件，但不是这样。</span><span class="sxs-lookup"><span data-stu-id="1957d-172">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="1957d-173">组件不能使用视图和特定于页的方案，如分部视图和节。</span><span class="sxs-lookup"><span data-stu-id="1957d-173">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="1957d-174">若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解为一个组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-174">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="1957d-175">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-175">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="1957d-176">有关如何呈现组件、组件状态和 `RenderComponentAsync` HTML 帮助器的详细信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="1957d-176">For more information on how components are rendered, component state, and the `RenderComponentAsync` HTML Helper, see <xref:blazor/hosting-models>.</span></span>

::: moniker-end

## <a name="use-components"></a><span data-ttu-id="1957d-177">使用组件</span><span class="sxs-lookup"><span data-stu-id="1957d-177">Use components</span></span>

<span data-ttu-id="1957d-178">组件可以通过使用 HTML 元素语法声明组件来包含其他组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-178">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="1957d-179">使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。</span><span class="sxs-lookup"><span data-stu-id="1957d-179">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="1957d-180">属性绑定区分大小写。</span><span class="sxs-lookup"><span data-stu-id="1957d-180">Attribute binding is case sensitive.</span></span> <span data-ttu-id="1957d-181">例如，`@bind` 是有效的，并且 `@Bind` 无效。</span><span class="sxs-lookup"><span data-stu-id="1957d-181">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="1957d-182">*Index*中的以下标记会呈现 `HeadingComponent` 实例：</span><span class="sxs-lookup"><span data-stu-id="1957d-182">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="1957d-183">*组件/HeadingComponent*：</span><span class="sxs-lookup"><span data-stu-id="1957d-183">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="1957d-184">如果某个组件包含一个 HTML 元素，该元素的首字母大写字母与组件名称不匹配，则会发出警告，指示该元素具有意外的名称。</span><span class="sxs-lookup"><span data-stu-id="1957d-184">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="1957d-185">添加组件命名空间的 `@using` 语句使组件可用，从而消除了警告。</span><span class="sxs-lookup"><span data-stu-id="1957d-185">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="1957d-186">组件参数</span><span class="sxs-lookup"><span data-stu-id="1957d-186">Component parameters</span></span>

<span data-ttu-id="1957d-187">组件可以具有*组件参数*，这些参数是使用组件类上的公共属性和 `[Parameter]` 特性定义的。</span><span class="sxs-lookup"><span data-stu-id="1957d-187">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="1957d-188">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-188">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="1957d-189">*组件/ChildComponent*：</span><span class="sxs-lookup"><span data-stu-id="1957d-189">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="1957d-190">在下面的示例应用程序示例中，`ParentComponent` 设置 `ChildComponent`的 `Title` 属性的值。</span><span class="sxs-lookup"><span data-stu-id="1957d-190">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="1957d-191">*Pages/ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="1957d-191">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="child-content"></a><span data-ttu-id="1957d-192">子内容</span><span class="sxs-lookup"><span data-stu-id="1957d-192">Child content</span></span>

<span data-ttu-id="1957d-193">组件可以设置另一个组件的内容。</span><span class="sxs-lookup"><span data-stu-id="1957d-193">Components can set the content of another component.</span></span> <span data-ttu-id="1957d-194">分配组件提供用于指定接收组件的标记之间的内容。</span><span class="sxs-lookup"><span data-stu-id="1957d-194">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="1957d-195">在下面的示例中，`ChildComponent` 具有一个表示 `RenderFragment`的 `ChildContent` 属性，该属性表示要呈现的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="1957d-195">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="1957d-196">`ChildContent` 的值放置在应呈现内容的组件标记中。</span><span class="sxs-lookup"><span data-stu-id="1957d-196">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="1957d-197">`ChildContent` 的值从父组件接收，并呈现在启动面板的 `panel-body`中。</span><span class="sxs-lookup"><span data-stu-id="1957d-197">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="1957d-198">*组件/ChildComponent*：</span><span class="sxs-lookup"><span data-stu-id="1957d-198">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="1957d-199">必须按约定将接收 `RenderFragment` 内容的属性命名为 `ChildContent`。</span><span class="sxs-lookup"><span data-stu-id="1957d-199">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="1957d-200">示例应用中的 `ParentComponent` 可以通过将内容置于 `<ChildComponent>` 标记中，提供用于呈现 `ChildComponent` 的内容。</span><span class="sxs-lookup"><span data-stu-id="1957d-200">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="1957d-201">*Pages/ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="1957d-201">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="1957d-202">Attribute 展开和任意参数</span><span class="sxs-lookup"><span data-stu-id="1957d-202">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="1957d-203">除了组件的声明参数外，组件还可以捕获和呈现附加属性。</span><span class="sxs-lookup"><span data-stu-id="1957d-203">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="1957d-204">其他属性可以在字典中捕获，然后在使用[`@attributes`](xref:mvc/views/razor#attributes) Razor 指令呈现组件时， *splatted*到元素上。</span><span class="sxs-lookup"><span data-stu-id="1957d-204">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="1957d-205">此方案在定义生成支持各种自定义项的标记元素的组件时非常有用。</span><span class="sxs-lookup"><span data-stu-id="1957d-205">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="1957d-206">例如，为支持多个参数的 `<input>` 分别定义属性可能比较繁琐。</span><span class="sxs-lookup"><span data-stu-id="1957d-206">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="1957d-207">在下面的示例中，第一个 `<input>` 元素（`id="useIndividualParams"`）使用单个组件参数，第二个 `<input>` 元素（`id="useAttributesDict"`）使用属性展开：</span><span class="sxs-lookup"><span data-stu-id="1957d-207">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="1957d-208">参数的类型必须实现具有字符串键 `IEnumerable<KeyValuePair<string, object>>`。</span><span class="sxs-lookup"><span data-stu-id="1957d-208">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="1957d-209">在这种情况下，也可以选择使用 `IReadOnlyDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="1957d-209">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="1957d-210">使用这两种方法呈现的 `<input>` 元素相同：</span><span class="sxs-lookup"><span data-stu-id="1957d-210">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="1957d-211">若要接受任意属性，请使用 `[Parameter]` 特性定义组件参数，并将 `CaptureUnmatchedValues` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="1957d-211">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="1957d-212">`[Parameter]` 上的 `CaptureUnmatchedValues` 属性允许参数匹配所有不匹配任何其他参数的属性。</span><span class="sxs-lookup"><span data-stu-id="1957d-212">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="1957d-213">组件只能使用 `CaptureUnmatchedValues`定义单个参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-213">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="1957d-214">与 `CaptureUnmatchedValues` 一起使用的属性类型必须从具有字符串键的 `Dictionary<string, object>` 中赋值。</span><span class="sxs-lookup"><span data-stu-id="1957d-214">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="1957d-215">此方案中也可以选择 `IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="1957d-215">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="1957d-216">相对于元素特性位置 `@attributes` 的位置很重要。</span><span class="sxs-lookup"><span data-stu-id="1957d-216">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="1957d-217">当在元素上 splatted `@attributes` 时，将从右到左（从上到下）处理特性。</span><span class="sxs-lookup"><span data-stu-id="1957d-217">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="1957d-218">请考虑以下示例：使用 `Child` 组件的组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-218">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="1957d-219">*ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="1957d-219">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="1957d-220">*ChildComponent*：</span><span class="sxs-lookup"><span data-stu-id="1957d-220">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="1957d-221">`Child` 组件的 `extra` 属性设置为 `@attributes`的右侧。</span><span class="sxs-lookup"><span data-stu-id="1957d-221">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="1957d-222">当通过附加属性传递时，`Parent` 组件的呈现 `<div>` 包含 `extra="5"`，因为属性从右到左处理（从上到第一）：</span><span class="sxs-lookup"><span data-stu-id="1957d-222">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="1957d-223">在下面的示例中，`Child` 组件的 `<div>`中反转 `extra` 和 `@attributes` 的顺序：</span><span class="sxs-lookup"><span data-stu-id="1957d-223">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="1957d-224">*ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="1957d-224">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="1957d-225">*ChildComponent*：</span><span class="sxs-lookup"><span data-stu-id="1957d-225">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="1957d-226">`Parent` 组件中呈现的 `<div>` 包含通过附加属性传递的 `extra="10"`：</span><span class="sxs-lookup"><span data-stu-id="1957d-226">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="data-binding"></a><span data-ttu-id="1957d-227">数据绑定</span><span class="sxs-lookup"><span data-stu-id="1957d-227">Data binding</span></span>

<span data-ttu-id="1957d-228">对组件和 DOM 元素的数据绑定都是通过[`@bind`](xref:mvc/views/razor#bind)特性来完成的。</span><span class="sxs-lookup"><span data-stu-id="1957d-228">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="1957d-229">下面的示例将 `CurrentValue` 属性绑定到文本框的值：</span><span class="sxs-lookup"><span data-stu-id="1957d-229">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1957d-230">当文本框失去焦点时，会更新该属性的值。</span><span class="sxs-lookup"><span data-stu-id="1957d-230">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="1957d-231">仅当呈现组件时，才会在 UI 中更新文本框，而不是响应更改属性值。</span><span class="sxs-lookup"><span data-stu-id="1957d-231">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="1957d-232">由于在事件处理程序代码执行后组件会自行呈现，因此在事件处理程序触发后，属性更新*通常*会立即反映在 UI 中。</span><span class="sxs-lookup"><span data-stu-id="1957d-232">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="1957d-233">结合使用 `@bind` 与 `CurrentValue` 属性（`<input @bind="CurrentValue" />`）本质上等效于以下内容：</span><span class="sxs-lookup"><span data-stu-id="1957d-233">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1957d-234">呈现组件时，输入元素的 `value` 来自 `CurrentValue` 属性。</span><span class="sxs-lookup"><span data-stu-id="1957d-234">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="1957d-235">当用户在文本框中键入内容并更改元素焦点时，将激发 `onchange` 事件并将 `CurrentValue` 属性设置为更改的值。</span><span class="sxs-lookup"><span data-stu-id="1957d-235">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="1957d-236">实际上，代码生成更复杂，因为 `@bind` 处理执行类型转换的情况。</span><span class="sxs-lookup"><span data-stu-id="1957d-236">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="1957d-237">原则上，`@bind` 将表达式的当前值与 `value` 属性相关联，并使用注册的处理程序来处理更改。</span><span class="sxs-lookup"><span data-stu-id="1957d-237">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="1957d-238">除了使用 `@bind` 语法处理 `onchange` 事件之外，还可以使用其他事件来绑定属性或字段，方法是使用 `event` 参数（[`@bind-value:event`](xref:mvc/views/razor#bind)）指定[`@bind-value`](xref:mvc/views/razor#bind)属性。</span><span class="sxs-lookup"><span data-stu-id="1957d-238">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="1957d-239">下面的示例将绑定 `oninput` 事件的 `CurrentValue` 属性：</span><span class="sxs-lookup"><span data-stu-id="1957d-239">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1957d-240">与 `onchange`不同，后者会在元素失去焦点时触发，`oninput` 在文本框的值更改时激发。</span><span class="sxs-lookup"><span data-stu-id="1957d-240">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="1957d-241">**不可分析值**</span><span class="sxs-lookup"><span data-stu-id="1957d-241">**Unparsable values**</span></span>

<span data-ttu-id="1957d-242">当用户向数据绑定元素提供无法分析的值时，触发绑定事件时，无法分析的值将自动恢复为其以前的值。</span><span class="sxs-lookup"><span data-stu-id="1957d-242">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="1957d-243">请考虑以下情形：</span><span class="sxs-lookup"><span data-stu-id="1957d-243">Consider the following scenario:</span></span>

* <span data-ttu-id="1957d-244">`<input>` 元素绑定到 `int` 类型，其初始值为 `123`：</span><span class="sxs-lookup"><span data-stu-id="1957d-244">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="1957d-245">用户更新元素的值以便在页面中 `123.45`，并更改元素焦点。</span><span class="sxs-lookup"><span data-stu-id="1957d-245">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="1957d-246">在上述方案中，将元素的值还原为 `123`。</span><span class="sxs-lookup"><span data-stu-id="1957d-246">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="1957d-247">如果拒绝值 `123.45` 以保证 `123`的原始值，则用户将知道其值不被接受。</span><span class="sxs-lookup"><span data-stu-id="1957d-247">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="1957d-248">默认情况下，绑定适用于元素的 `onchange` 事件（`@bind="{PROPERTY OR FIELD}"`）。</span><span class="sxs-lookup"><span data-stu-id="1957d-248">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="1957d-249">使用 `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` 设置不同的事件。</span><span class="sxs-lookup"><span data-stu-id="1957d-249">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="1957d-250">对于 `oninput` 事件（`@bind-value:event="oninput"`），重新确定会在引入了可分析值的任何击键之后发生。</span><span class="sxs-lookup"><span data-stu-id="1957d-250">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="1957d-251">当以 `int`绑定类型为 `oninput` 事件定位时，将阻止用户键入 `.` 字符。</span><span class="sxs-lookup"><span data-stu-id="1957d-251">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="1957d-252">会立即删除 `.` 字符，因此用户会立即收到仅允许整数的反馈。</span><span class="sxs-lookup"><span data-stu-id="1957d-252">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="1957d-253">在某些情况下，在 `oninput` 事件上恢复该值的情况并不理想，例如当用户应允许清除无法解析的 `<input>` 值时。</span><span class="sxs-lookup"><span data-stu-id="1957d-253">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="1957d-254">替代项包括：</span><span class="sxs-lookup"><span data-stu-id="1957d-254">Alternatives include:</span></span>

* <span data-ttu-id="1957d-255">不要使用 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="1957d-255">Don't use the `oninput` event.</span></span> <span data-ttu-id="1957d-256">使用默认 `onchange` 事件（`@bind="{PROPERTY OR FIELD}"`），其中无效值直到元素失去焦点时才会被还原。</span><span class="sxs-lookup"><span data-stu-id="1957d-256">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="1957d-257">绑定到可为 null 的类型（如 `int?` 或 `string`），并提供自定义逻辑来处理无效项。</span><span class="sxs-lookup"><span data-stu-id="1957d-257">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="1957d-258">使用[窗体验证组件](xref:blazor/forms-validation)，如 `InputNumber` 或 `InputDate`。</span><span class="sxs-lookup"><span data-stu-id="1957d-258">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="1957d-259">窗体验证组件具有用于管理无效输入的内置支持。</span><span class="sxs-lookup"><span data-stu-id="1957d-259">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="1957d-260">窗体验证组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-260">Form validation components:</span></span>
  * <span data-ttu-id="1957d-261">允许用户提供无效输入并在关联的 `EditContext`上收到验证错误。</span><span class="sxs-lookup"><span data-stu-id="1957d-261">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="1957d-262">在 UI 中显示验证错误，不干扰用户输入其他 webform 数据。</span><span class="sxs-lookup"><span data-stu-id="1957d-262">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="1957d-263">**全球化**</span><span class="sxs-lookup"><span data-stu-id="1957d-263">**Globalization**</span></span>

<span data-ttu-id="1957d-264">`@bind` 值的格式设置为显示，并使用当前区域性的规则进行分析。</span><span class="sxs-lookup"><span data-stu-id="1957d-264">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="1957d-265">可以从 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 属性访问当前区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-265">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="1957d-266">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)用于以下字段类型（`<input type="{TYPE}" />`）：</span><span class="sxs-lookup"><span data-stu-id="1957d-266">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="1957d-267">上述字段类型：</span><span class="sxs-lookup"><span data-stu-id="1957d-267">The preceding field types:</span></span>

* <span data-ttu-id="1957d-268">使用其基于浏览器的适当格式规则显示。</span><span class="sxs-lookup"><span data-stu-id="1957d-268">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="1957d-269">不能包含自由格式的文本。</span><span class="sxs-lookup"><span data-stu-id="1957d-269">Can't contain free-form text.</span></span>
* <span data-ttu-id="1957d-270">基于浏览器的实现提供用户交互特性。</span><span class="sxs-lookup"><span data-stu-id="1957d-270">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="1957d-271">以下字段类型具有特定的格式要求，当前不受 Blazor 支持，因为所有主要浏览器不支持它们：</span><span class="sxs-lookup"><span data-stu-id="1957d-271">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="1957d-272">`@bind` 支持 `@bind:culture` 参数，以提供用于分析值并设置其格式的 <xref:System.Globalization.CultureInfo?displayProperty=fullName>。</span><span class="sxs-lookup"><span data-stu-id="1957d-272">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="1957d-273">使用 `date` 和 `number` 字段类型时，不建议指定区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-273">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="1957d-274">`date` 和 `number` 提供提供所需区域性的内置 Blazor 支持。</span><span class="sxs-lookup"><span data-stu-id="1957d-274">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="1957d-275">有关如何设置用户的区域性的信息，请参阅[本地化](#localization)部分。</span><span class="sxs-lookup"><span data-stu-id="1957d-275">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="1957d-276">**格式字符串**</span><span class="sxs-lookup"><span data-stu-id="1957d-276">**Format strings**</span></span>

<span data-ttu-id="1957d-277">数据绑定使用[`@bind:format`](xref:mvc/views/razor#bind)<xref:System.DateTime> 格式字符串。</span><span class="sxs-lookup"><span data-stu-id="1957d-277">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="1957d-278">现在不能使用其他格式的表达式，如货币或数字格式。</span><span class="sxs-lookup"><span data-stu-id="1957d-278">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="1957d-279">在前面的代码中，`<input>` 元素的字段类型（`type`）默认为 `text`。</span><span class="sxs-lookup"><span data-stu-id="1957d-279">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="1957d-280">支持 `@bind:format` 绑定以下 .NET 类型：</span><span class="sxs-lookup"><span data-stu-id="1957d-280">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="1957d-281"><xref:System.DateTime?displayProperty=fullName>？</span><span class="sxs-lookup"><span data-stu-id="1957d-281"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="1957d-282"><xref:System.DateTimeOffset?displayProperty=fullName>？</span><span class="sxs-lookup"><span data-stu-id="1957d-282"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="1957d-283">`@bind:format` 特性指定要应用于 `<input>` 元素的 `value` 的日期格式。</span><span class="sxs-lookup"><span data-stu-id="1957d-283">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="1957d-284">此格式还用于分析 `onchange` 事件发生时的值。</span><span class="sxs-lookup"><span data-stu-id="1957d-284">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="1957d-285">不建议为 `date` 字段类型指定格式，因为 Blazor 具有对日期进行格式设置的内置支持。</span><span class="sxs-lookup"><span data-stu-id="1957d-285">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="1957d-286">尽管建议，但如果使用 `date` 字段类型提供格式，则仅使用 `yyyy-MM-dd` 日期格式才能正常工作：</span><span class="sxs-lookup"><span data-stu-id="1957d-286">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

<span data-ttu-id="1957d-287">**组件参数**</span><span class="sxs-lookup"><span data-stu-id="1957d-287">**Component parameters**</span></span>

<span data-ttu-id="1957d-288">绑定识别组件参数，其中 `@bind-{property}` 可以跨组件绑定属性值。</span><span class="sxs-lookup"><span data-stu-id="1957d-288">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="1957d-289">以下子组件（`ChildComponent`）具有 `Year` 组件参数和 `YearChanged` 回调：</span><span class="sxs-lookup"><span data-stu-id="1957d-289">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="1957d-290">[EventCallback](#eventcallback)部分介绍了 `EventCallback<T>`。</span><span class="sxs-lookup"><span data-stu-id="1957d-290">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="1957d-291">以下父组件使用 `ChildComponent`，并将 `ParentYear` 参数从父级绑定到子组件上的 `Year` 参数：</span><span class="sxs-lookup"><span data-stu-id="1957d-291">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```razor
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

<span data-ttu-id="1957d-292">加载 `ParentComponent` 会生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="1957d-292">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="1957d-293">如果通过在 `ParentComponent`中选择按钮更改了 `ParentYear` 属性的值，则将更新 `ChildComponent` 的 `Year` 属性。</span><span class="sxs-lookup"><span data-stu-id="1957d-293">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="1957d-294">当 `ParentComponent` 重新呈现时，`Year` 的新值将呈现在 UI 中：</span><span class="sxs-lookup"><span data-stu-id="1957d-294">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="1957d-295">`Year` 参数是可绑定的，因为它具有与 `Year` 参数类型相匹配的伴随 `YearChanged` 事件。</span><span class="sxs-lookup"><span data-stu-id="1957d-295">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="1957d-296">按照约定，`<ChildComponent @bind-Year="ParentYear" />` 本质上等同于编写：</span><span class="sxs-lookup"><span data-stu-id="1957d-296">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="1957d-297">通常，可以使用 `@bind-property:event` 特性将属性绑定到相应的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="1957d-297">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="1957d-298">例如，可以使用以下两个属性将属性 `MyProp` 绑定到 `MyEventHandler`：</span><span class="sxs-lookup"><span data-stu-id="1957d-298">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="1957d-299">事件处理</span><span class="sxs-lookup"><span data-stu-id="1957d-299">Event handling</span></span>

<span data-ttu-id="1957d-300">Razor 组件提供事件处理功能。</span><span class="sxs-lookup"><span data-stu-id="1957d-300">Razor components provide event handling features.</span></span> <span data-ttu-id="1957d-301">对于名为 `on{EVENT}` 的 HTML 元素特性（例如，`onclick` 和 `onsubmit`）与委托类型的值，Razor 组件将属性值视为事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="1957d-301">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="1957d-302">特性的名称始终[`@on{EVENT}`](xref:mvc/views/razor#onevent)格式。</span><span class="sxs-lookup"><span data-stu-id="1957d-302">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="1957d-303">当在 UI 中选择该按钮时，以下代码将调用 `UpdateHeading` 方法：</span><span class="sxs-lookup"><span data-stu-id="1957d-303">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
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

<span data-ttu-id="1957d-304">下面的代码在 UI 中的复选框发生更改时调用 `CheckChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="1957d-304">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="1957d-305">事件处理程序也可以是异步的，并返回 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="1957d-305">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="1957d-306">无需手动调用[StateHasChanged](xref:blazor/lifecycle#state-changes)。</span><span class="sxs-lookup"><span data-stu-id="1957d-306">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="1957d-307">异常在发生时进行记录。</span><span class="sxs-lookup"><span data-stu-id="1957d-307">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="1957d-308">在下面的示例中，当选择按钮时，将异步调用 `UpdateHeading`：</span><span class="sxs-lookup"><span data-stu-id="1957d-308">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
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

### <a name="event-argument-types"></a><span data-ttu-id="1957d-309">事件参数类型</span><span class="sxs-lookup"><span data-stu-id="1957d-309">Event argument types</span></span>

<span data-ttu-id="1957d-310">对于某些事件，允许使用事件参数类型。</span><span class="sxs-lookup"><span data-stu-id="1957d-310">For some events, event argument types are permitted.</span></span> <span data-ttu-id="1957d-311">如果不需要访问这些事件类型之一，则方法调用中不需要这样做。</span><span class="sxs-lookup"><span data-stu-id="1957d-311">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="1957d-312">下表显示了支持的 `EventArgs`。</span><span class="sxs-lookup"><span data-stu-id="1957d-312">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="1957d-313">Event</span><span class="sxs-lookup"><span data-stu-id="1957d-313">Event</span></span>            | <span data-ttu-id="1957d-314">类</span><span class="sxs-lookup"><span data-stu-id="1957d-314">Class</span></span>                | <span data-ttu-id="1957d-315">DOM 事件和说明</span><span class="sxs-lookup"><span data-stu-id="1957d-315">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="1957d-316">剪贴板</span><span class="sxs-lookup"><span data-stu-id="1957d-316">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="1957d-317">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="1957d-317">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="1957d-318">拖动</span><span class="sxs-lookup"><span data-stu-id="1957d-318">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="1957d-319">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="1957d-319">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="1957d-320">`DataTransfer` 和 `DataTransferItem` 保存拖动的项数据。</span><span class="sxs-lookup"><span data-stu-id="1957d-320">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="1957d-321">错误</span><span class="sxs-lookup"><span data-stu-id="1957d-321">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="1957d-322">Event</span><span class="sxs-lookup"><span data-stu-id="1957d-322">Event</span></span>            | `EventArgs`          | <span data-ttu-id="1957d-323">*常规*</span><span class="sxs-lookup"><span data-stu-id="1957d-323">*General*</span></span><br><span data-ttu-id="1957d-324">`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`</span><span class="sxs-lookup"><span data-stu-id="1957d-324">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="1957d-325">*剪贴板*</span><span class="sxs-lookup"><span data-stu-id="1957d-325">*Clipboard*</span></span><br><span data-ttu-id="1957d-326">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="1957d-326">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="1957d-327">*输入*</span><span class="sxs-lookup"><span data-stu-id="1957d-327">*Input*</span></span><br><span data-ttu-id="1957d-328">`oninvalid`、 `onreset`、 `onselect`、 `onselectionchange`、 `onselectstart`、 `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="1957d-328">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="1957d-329">*媒体*</span><span class="sxs-lookup"><span data-stu-id="1957d-329">*Media*</span></span><br><span data-ttu-id="1957d-330">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting`</span><span class="sxs-lookup"><span data-stu-id="1957d-330">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="1957d-331">专注</span><span class="sxs-lookup"><span data-stu-id="1957d-331">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="1957d-332">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="1957d-332">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="1957d-333">不包含对 `relatedTarget`的支持。</span><span class="sxs-lookup"><span data-stu-id="1957d-333">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="1957d-334">输入</span><span class="sxs-lookup"><span data-stu-id="1957d-334">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="1957d-335">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="1957d-335">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="1957d-336">键盘</span><span class="sxs-lookup"><span data-stu-id="1957d-336">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="1957d-337">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="1957d-337">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="1957d-338">鼠标</span><span class="sxs-lookup"><span data-stu-id="1957d-338">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="1957d-339">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="1957d-339">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="1957d-340">鼠标指针</span><span class="sxs-lookup"><span data-stu-id="1957d-340">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="1957d-341">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="1957d-341">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="1957d-342">鼠标滚轮</span><span class="sxs-lookup"><span data-stu-id="1957d-342">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="1957d-343">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="1957d-343">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="1957d-344">爬网</span><span class="sxs-lookup"><span data-stu-id="1957d-344">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="1957d-345">`onabort`、 `onload`、 `onloadend`、 `onloadstart`、 `onprogress`、 `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="1957d-345">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="1957d-346">触控</span><span class="sxs-lookup"><span data-stu-id="1957d-346">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="1957d-347">`ontouchstart`、 `ontouchend`、 `ontouchmove`、 `ontouchenter`、 `ontouchleave`、 `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="1957d-347">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="1957d-348">`TouchPoint` 表示触控相关设备上的单个联系点。</span><span class="sxs-lookup"><span data-stu-id="1957d-348">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="1957d-349">有关上表中事件的属性和事件处理行为的信息，请参阅[引用源中的 EventArgs 类（aspnet/AspNetCore release/3.0 分支）](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="1957d-349">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="1957d-350">Lambda 表达式</span><span class="sxs-lookup"><span data-stu-id="1957d-350">Lambda expressions</span></span>

<span data-ttu-id="1957d-351">还可以使用 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="1957d-351">Lambda expressions can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="1957d-352">通常可以很方便地关闭其他值，如在循环访问一组元素时。</span><span class="sxs-lookup"><span data-stu-id="1957d-352">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="1957d-353">下面的示例创建了三个按钮，每个按钮都 `UpdateHeading` 在用户界面中选择时传递事件参数（`MouseEventArgs`）和按钮号（`buttonNumber`）：</span><span class="sxs-lookup"><span data-stu-id="1957d-353">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
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
> <span data-ttu-id="1957d-354">不要直接在 lambda 表达式**中使用 `for`** 循环中的循环变量（`i`）。</span><span class="sxs-lookup"><span data-stu-id="1957d-354">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="1957d-355">否则，所有 lambda 表达式都将使用相同的变量，导致 `i`的值在所有 lambda 中都相同。</span><span class="sxs-lookup"><span data-stu-id="1957d-355">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="1957d-356">始终捕获其在本地变量中的值（在前面的示例中为`buttonNumber`），然后使用它。</span><span class="sxs-lookup"><span data-stu-id="1957d-356">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="1957d-357">EventCallback</span><span class="sxs-lookup"><span data-stu-id="1957d-357">EventCallback</span></span>

<span data-ttu-id="1957d-358">使用嵌套组件的常见方案是，当发生子组件事件时，需要运行父组件的方法&mdash;例如，在子组件发生 `onclick` 事件时。</span><span class="sxs-lookup"><span data-stu-id="1957d-358">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="1957d-359">若要跨组件公开事件，请使用 `EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="1957d-359">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="1957d-360">父组件可将回调方法分配给子组件的 `EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="1957d-360">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="1957d-361">示例应用（*ChildComponent*）中的 `ChildComponent` 演示如何设置按钮的 `onclick` 处理程序，以便从示例的 `ParentComponent`接收 `EventCallback` 委托。</span><span class="sxs-lookup"><span data-stu-id="1957d-361">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="1957d-362">`EventCallback` 是使用 `MouseEventArgs`键入的，这适用于来自外围设备的 `onclick` 事件：</span><span class="sxs-lookup"><span data-stu-id="1957d-362">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="1957d-363">`ParentComponent` 将子级的 `EventCallback<T>` 设置为其 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="1957d-363">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method.</span></span>

<span data-ttu-id="1957d-364">*Pages/ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="1957d-364">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="1957d-365">在 `ChildComponent`中选择该按钮时：</span><span class="sxs-lookup"><span data-stu-id="1957d-365">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="1957d-366">调用 `ParentComponent`的 `ShowMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="1957d-366">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="1957d-367">`messageText` 会更新并显示在 `ParentComponent`中。</span><span class="sxs-lookup"><span data-stu-id="1957d-367">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="1957d-368">回调的方法（`ShowMessage`）中不需要调用[StateHasChanged](xref:blazor/lifecycle#state-changes) 。</span><span class="sxs-lookup"><span data-stu-id="1957d-368">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="1957d-369">`StateHasChanged` 将自动调用以诸如此类 `ParentComponent`，就像子事件在子中执行的事件处理程序中 rerendering。</span><span class="sxs-lookup"><span data-stu-id="1957d-369">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="1957d-370">`EventCallback` 和 `EventCallback<T>` 允许异步委托。</span><span class="sxs-lookup"><span data-stu-id="1957d-370">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="1957d-371">`EventCallback<T>` 为强类型，并且需要特定的参数类型。</span><span class="sxs-lookup"><span data-stu-id="1957d-371">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="1957d-372">`EventCallback` 弱类型化，并允许任何参数类型。</span><span class="sxs-lookup"><span data-stu-id="1957d-372">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="1957d-373">使用 `InvokeAsync` 调用 `EventCallback` 或 `EventCallback<T>`，并等待 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="1957d-373">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="1957d-374">使用 `EventCallback` 和 `EventCallback<T>` 进行事件处理和绑定组件参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-374">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="1957d-375">优先使用强类型 `EventCallback<T>` `EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="1957d-375">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="1957d-376">`EventCallback<T>` 向组件用户提供更好的错误反馈。</span><span class="sxs-lookup"><span data-stu-id="1957d-376">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="1957d-377">与其他 UI 事件处理程序类似，指定事件参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="1957d-377">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="1957d-378">当没有值传递到回调时，使用 `EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="1957d-378">Use `EventCallback` when there's no value passed to the callback.</span></span>

::: moniker range=">= aspnetcore-3.1"

### <a name="prevent-default-actions"></a><span data-ttu-id="1957d-379">阻止默认操作</span><span class="sxs-lookup"><span data-stu-id="1957d-379">Prevent default actions</span></span>

<span data-ttu-id="1957d-380">使用[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)指令特性可防止事件的默认操作。</span><span class="sxs-lookup"><span data-stu-id="1957d-380">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="1957d-381">如果在输入设备上选择了某个键，并且该元素焦点位于某个文本框上，则浏览器通常会在文本框中显示该键的字符。</span><span class="sxs-lookup"><span data-stu-id="1957d-381">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="1957d-382">在下面的示例中，通过指定 `@onkeypress:preventDefault` 指令特性来阻止默认行为。</span><span class="sxs-lookup"><span data-stu-id="1957d-382">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="1957d-383">计数器会递增，并且不会将 **+** 键捕获到 `<input>` 元素的值中：</span><span class="sxs-lookup"><span data-stu-id="1957d-383">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

<span data-ttu-id="1957d-384">在不使用值的情况下指定 `@on{EVENT}:preventDefault` 特性等效于 `@on{EVENT}:preventDefault="true"`。</span><span class="sxs-lookup"><span data-stu-id="1957d-384">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="1957d-385">特性的值还可以是表达式。</span><span class="sxs-lookup"><span data-stu-id="1957d-385">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="1957d-386">在下面的示例中，`_shouldPreventDefault` 是设置为 `true` 或 `false`的 `bool` 字段：</span><span class="sxs-lookup"><span data-stu-id="1957d-386">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="1957d-387">不需要使用事件处理程序来防止默认操作。</span><span class="sxs-lookup"><span data-stu-id="1957d-387">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="1957d-388">可以单独使用事件处理程序和阻止默认操作方案。</span><span class="sxs-lookup"><span data-stu-id="1957d-388">The event handler and prevent default action scenarios can be used independently.</span></span>

### <a name="stop-event-propagation"></a><span data-ttu-id="1957d-389">停止事件传播</span><span class="sxs-lookup"><span data-stu-id="1957d-389">Stop event propagation</span></span>

<span data-ttu-id="1957d-390">使用[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)指令特性来停止事件传播。</span><span class="sxs-lookup"><span data-stu-id="1957d-390">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="1957d-391">在下面的示例中，选中此复选框可阻止单击第二个子 `<div>` 中的事件传播到父 `<div>`：</span><span class="sxs-lookup"><span data-stu-id="1957d-391">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker-end

## <a name="chained-bind"></a><span data-ttu-id="1957d-392">链式绑定</span><span class="sxs-lookup"><span data-stu-id="1957d-392">Chained bind</span></span>

<span data-ttu-id="1957d-393">常见的情况是将数据绑定参数链接到组件输出中的页元素。</span><span class="sxs-lookup"><span data-stu-id="1957d-393">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="1957d-394">此方案称为*链接绑定*，因为多个级别的绑定同时发生。</span><span class="sxs-lookup"><span data-stu-id="1957d-394">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="1957d-395">无法使用页面元素中 `@bind` 语法来实现链接绑定。</span><span class="sxs-lookup"><span data-stu-id="1957d-395">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="1957d-396">必须单独指定事件处理程序和值。</span><span class="sxs-lookup"><span data-stu-id="1957d-396">The event handler and value must be specified separately.</span></span> <span data-ttu-id="1957d-397">但是，父组件可以将 `@bind` 语法与组件的参数一起使用。</span><span class="sxs-lookup"><span data-stu-id="1957d-397">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="1957d-398">以下 `PasswordField` 组件（*self.passwordfield.text*）：</span><span class="sxs-lookup"><span data-stu-id="1957d-398">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="1957d-399">将 `<input>` 元素的值设置为 `Password` 属性。</span><span class="sxs-lookup"><span data-stu-id="1957d-399">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="1957d-400">使用[EventCallback](#eventcallback)向父组件公开 `Password` 属性的更改。</span><span class="sxs-lookup"><span data-stu-id="1957d-400">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```razor
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

<span data-ttu-id="1957d-401">`PasswordField` 组件用于另一个组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-401">The `PasswordField` component is used in another component:</span></span>

```razor
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="1957d-402">对上述示例中的密码执行检查或陷阱错误：</span><span class="sxs-lookup"><span data-stu-id="1957d-402">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="1957d-403">为 `Password` 创建支持字段（在下面的示例代码中`password`）。</span><span class="sxs-lookup"><span data-stu-id="1957d-403">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="1957d-404">在 `Password` 资源库中执行检查或陷阱错误。</span><span class="sxs-lookup"><span data-stu-id="1957d-404">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="1957d-405">如果密码的值中使用了空格，则以下示例向用户提供即时反馈：</span><span class="sxs-lookup"><span data-stu-id="1957d-405">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
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

## <a name="capture-references-to-components"></a><span data-ttu-id="1957d-406">捕获对组件的引用</span><span class="sxs-lookup"><span data-stu-id="1957d-406">Capture references to components</span></span>

<span data-ttu-id="1957d-407">组件引用提供了一种方法来引用组件实例，以便可以向该实例发出命令，如 `Show` 或 `Reset`。</span><span class="sxs-lookup"><span data-stu-id="1957d-407">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="1957d-408">捕获组件引用：</span><span class="sxs-lookup"><span data-stu-id="1957d-408">To capture a component reference:</span></span>

* <span data-ttu-id="1957d-409">向子组件添加[`@ref`](xref:mvc/views/razor#ref)特性。</span><span class="sxs-lookup"><span data-stu-id="1957d-409">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="1957d-410">定义与子组件类型相同的字段。</span><span class="sxs-lookup"><span data-stu-id="1957d-410">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="1957d-411">呈现组件时，将用 `MyLoginDialog` 子组件实例填充 `loginDialog` 字段。</span><span class="sxs-lookup"><span data-stu-id="1957d-411">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="1957d-412">然后，可以在组件实例上调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="1957d-412">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1957d-413">仅在呈现组件后填充 `loginDialog` 变量，其输出包含 `MyLoginDialog` 元素。</span><span class="sxs-lookup"><span data-stu-id="1957d-413">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="1957d-414">在该点之前，没有任何内容可供参考。</span><span class="sxs-lookup"><span data-stu-id="1957d-414">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="1957d-415">若要在组件完成呈现后操作组件引用，请使用[OnAfterRenderAsync 或 OnAfterRender 方法](xref:blazor/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="1957d-415">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="1957d-416">捕获组件引用时，请使用类似的语法来[捕获元素引用](xref:blazor/javascript-interop#capture-references-to-elements)，而不是[JavaScript 互操作](xref:blazor/javascript-interop)功能。</span><span class="sxs-lookup"><span data-stu-id="1957d-416">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="1957d-417">不会将组件引用传递给 JavaScript 代码&mdash;它们只在 .NET 代码中使用。</span><span class="sxs-lookup"><span data-stu-id="1957d-417">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="1957d-418">不要**使用组件**引用来改变子组件的状态。</span><span class="sxs-lookup"><span data-stu-id="1957d-418">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="1957d-419">请改用常规声明性参数将数据传递给子组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-419">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="1957d-420">使用正常的声明性参数会导致子组件自动诸如此类正确的时间。</span><span class="sxs-lookup"><span data-stu-id="1957d-420">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="1957d-421">在外部调用组件方法以更新状态</span><span class="sxs-lookup"><span data-stu-id="1957d-421">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="1957d-422"> 使用 `SynchronizationContext` 来强制执行单个逻辑线程。</span><span class="sxs-lookup"><span data-stu-id="1957d-422"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="1957d-423">在此 `SynchronizationContext`上执行由 Blazor 引发的组件[生命周期方法](xref:blazor/lifecycle)和任何事件回调。</span><span class="sxs-lookup"><span data-stu-id="1957d-423">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="1957d-424">如果组件必须根据外部事件（如计时器或其他通知）进行更新，请使用 `InvokeAsync` 方法，该方法将调度到 Blazor的 `SynchronizationContext`。</span><span class="sxs-lookup"><span data-stu-id="1957d-424">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="1957d-425">例如，假设有一个*通告程序服务*可以通知已更新状态的任何侦听组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-425">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="1957d-426">使用 `NotifierService` 更新组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-426">Usage of the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="1957d-427">在前面的示例中，`NotifierService` 在 Blazor的 `SynchronizationContext`之外调用组件的 `OnNotify` 方法。</span><span class="sxs-lookup"><span data-stu-id="1957d-427">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="1957d-428">`InvokeAsync` 用于切换到正确的上下文，并将呈现器排队。</span><span class="sxs-lookup"><span data-stu-id="1957d-428">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="1957d-429">使用 \@键控制是否保留元素和组件</span><span class="sxs-lookup"><span data-stu-id="1957d-429">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="1957d-430">在呈现元素或组件的列表并且元素或组件随后发生变化时，Blazor的比较算法必须决定哪些之前的元素或组件可以保留，以及模型对象应如何映射到它们。</span><span class="sxs-lookup"><span data-stu-id="1957d-430">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="1957d-431">通常，此过程是自动的，可以忽略，但在某些情况下，您可能需要控制该过程。</span><span class="sxs-lookup"><span data-stu-id="1957d-431">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="1957d-432">请看下面的示例：</span><span class="sxs-lookup"><span data-stu-id="1957d-432">Consider the following example:</span></span>

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

<span data-ttu-id="1957d-433">`People` 集合的内容可能会随插入、删除或重新排序条目而更改。</span><span class="sxs-lookup"><span data-stu-id="1957d-433">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="1957d-434">当组件 rerenders 时，`<DetailsEditor>` 组件可能会更改以接收不同 `Details` 参数值。</span><span class="sxs-lookup"><span data-stu-id="1957d-434">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="1957d-435">这可能导致比预期更复杂的 rerendering。</span><span class="sxs-lookup"><span data-stu-id="1957d-435">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="1957d-436">在某些情况下，rerendering 可能会导致可见行为差异，如失去元素焦点。</span><span class="sxs-lookup"><span data-stu-id="1957d-436">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="1957d-437">可以通过 `@key` 指令特性来控制映射过程。</span><span class="sxs-lookup"><span data-stu-id="1957d-437">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="1957d-438">`@key` 使比较算法根据键的值保证保留元素或组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-438">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="1957d-439">当 `People` 集合发生更改时，比较算法会保留 `<DetailsEditor>` 实例与 `person` 实例之间的关联：</span><span class="sxs-lookup"><span data-stu-id="1957d-439">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="1957d-440">如果从 `People` 列表中删除 `Person`，则仅从 UI 中删除相应的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="1957d-440">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="1957d-441">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="1957d-441">Other instances are left unchanged.</span></span>
* <span data-ttu-id="1957d-442">如果在列表中的某个位置插入 `Person`，则会在相应位置插入一个新的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="1957d-442">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="1957d-443">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="1957d-443">Other instances are left unchanged.</span></span>
* <span data-ttu-id="1957d-444">如果重新排序 `Person` 项，则将保留相应的 `<DetailsEditor>` 实例，并在 UI 中重新排序。</span><span class="sxs-lookup"><span data-stu-id="1957d-444">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="1957d-445">在某些情况下，使用 `@key` 最大程度地降低了 rerendering 的复杂性，并避免了在 DOM 的有状态部分发生变化时出现潜在问题，如焦点位置。</span><span class="sxs-lookup"><span data-stu-id="1957d-445">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1957d-446">键对于每个容器元素或组件都是本地的。</span><span class="sxs-lookup"><span data-stu-id="1957d-446">Keys are local to each container element or component.</span></span> <span data-ttu-id="1957d-447">不会在整个文档中全局地比较键。</span><span class="sxs-lookup"><span data-stu-id="1957d-447">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="1957d-448">何时使用 \@密钥</span><span class="sxs-lookup"><span data-stu-id="1957d-448">When to use \@key</span></span>

<span data-ttu-id="1957d-449">通常，每当呈现列表时（例如，在 `@foreach` 块中）和适当的值（用于定义 `@key`），都有必要使用 `@key`。</span><span class="sxs-lookup"><span data-stu-id="1957d-449">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="1957d-450">还可以使用 `@key` 来防止 Blazor 在对象发生更改时保留元素或组件子树：</span><span class="sxs-lookup"><span data-stu-id="1957d-450">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="1957d-451">如果 `@currentPerson` 更改，则 `@key` attribute 指令强制 Blazor 丢弃整个 `<div>` 及其后代，并利用新的元素和组件重新生成 UI 中的子树。</span><span class="sxs-lookup"><span data-stu-id="1957d-451">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="1957d-452">如果需要确保在 `@currentPerson` 更改时不保留 UI 状态，这会很有用。</span><span class="sxs-lookup"><span data-stu-id="1957d-452">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="1957d-453">何时不使用 \@键</span><span class="sxs-lookup"><span data-stu-id="1957d-453">When not to use \@key</span></span>

<span data-ttu-id="1957d-454">与 `@key`进行比较时，性能会降低。</span><span class="sxs-lookup"><span data-stu-id="1957d-454">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="1957d-455">性能开销不是很大，但只有在控制元素或组件保留规则对应用有利的情况下才指定 `@key`。</span><span class="sxs-lookup"><span data-stu-id="1957d-455">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="1957d-456">即使未使用 `@key`，Blazor 也会尽可能地保留子元素和组件实例。</span><span class="sxs-lookup"><span data-stu-id="1957d-456">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="1957d-457">使用 `@key` 的唯一优点是控制*如何*将模型实例映射到保留的组件实例，而不是选择映射的比较算法。</span><span class="sxs-lookup"><span data-stu-id="1957d-457">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="1957d-458">\@键使用哪些值</span><span class="sxs-lookup"><span data-stu-id="1957d-458">What values to use for \@key</span></span>

<span data-ttu-id="1957d-459">通常，为 `@key`提供以下类型之一：</span><span class="sxs-lookup"><span data-stu-id="1957d-459">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="1957d-460">模型对象实例（例如，在前面的示例中，`Person` 实例）。</span><span class="sxs-lookup"><span data-stu-id="1957d-460">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="1957d-461">这可确保基于对象引用相等性保存。</span><span class="sxs-lookup"><span data-stu-id="1957d-461">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="1957d-462">唯一标识符（例如，类型的主键值 `int`、`string`或 `Guid`）。</span><span class="sxs-lookup"><span data-stu-id="1957d-462">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="1957d-463">确保用于 `@key` 的值不冲突。</span><span class="sxs-lookup"><span data-stu-id="1957d-463">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="1957d-464">如果在同一父元素内检测到冲突值，Blazor 引发异常，因为它无法确定将旧元素或组件映射到新元素或组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-464">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="1957d-465">仅使用非重复值，例如对象实例或主键值。</span><span class="sxs-lookup"><span data-stu-id="1957d-465">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="routing"></a><span data-ttu-id="1957d-466">路由</span><span class="sxs-lookup"><span data-stu-id="1957d-466">Routing</span></span>

<span data-ttu-id="1957d-467">可以通过为应用中的每个可访问组件提供路由模板来实现 Blazor 中的路由。</span><span class="sxs-lookup"><span data-stu-id="1957d-467">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="1957d-468">编译具有 `@page` 指令的 Razor 文件时，将为生成的类指定 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 指定路由模板的。</span><span class="sxs-lookup"><span data-stu-id="1957d-468">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="1957d-469">在运行时，路由器将使用 `RouteAttribute` 查找组件类，并呈现哪个组件包含与请求的 URL 匹配的路由模板。</span><span class="sxs-lookup"><span data-stu-id="1957d-469">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="1957d-470">可以将多个路由模板应用于组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-470">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="1957d-471">以下组件响应 `/BlazorRoute` 和 `/DifferentBlazorRoute`的请求。</span><span class="sxs-lookup"><span data-stu-id="1957d-471">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="1957d-472">*Pages/BlazorRoute*：</span><span class="sxs-lookup"><span data-stu-id="1957d-472">*Pages/BlazorRoute.razor*:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a><span data-ttu-id="1957d-473">路由参数</span><span class="sxs-lookup"><span data-stu-id="1957d-473">Route parameters</span></span>

<span data-ttu-id="1957d-474">组件可以接收 `@page` 指令中提供的路由模板中的路由参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-474">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="1957d-475">路由器使用路由参数来填充相应的组件参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-475">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="1957d-476">*Pages/RouteParameter*：</span><span class="sxs-lookup"><span data-stu-id="1957d-476">*Pages/RouteParameter.razor*:</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="1957d-477">不支持可选参数，因此在上面的示例中应用了两个 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="1957d-477">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="1957d-478">第一个允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-478">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="1957d-479">第二个 `@page` 指令采用 `{text}` 路由参数，并将该值分配给 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="1957d-479">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="1957d-480">*捕获所有*参数语法（`*`/`**`），该语法跨多个文件夹边界捕获路径，而 razor 组件（*razor*）**不**支持此语法。</span><span class="sxs-lookup"><span data-stu-id="1957d-480">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="partial-class-support"></a><span data-ttu-id="1957d-481">分部类支持</span><span class="sxs-lookup"><span data-stu-id="1957d-481">Partial class support</span></span>

<span data-ttu-id="1957d-482">Razor 组件以分部类的形式生成。</span><span class="sxs-lookup"><span data-stu-id="1957d-482">Razor components are generated as partial classes.</span></span> <span data-ttu-id="1957d-483">使用以下方法之一创作 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-483">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="1957d-484">C#在一个文件中使用 HTML 标记和 Razor 代码在[`@code`](xref:mvc/views/razor#code)块中定义代码。</span><span class="sxs-lookup"><span data-stu-id="1957d-484">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="1957d-485"> 模板使用此方法来定义其 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-485"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="1957d-486">C#代码位于定义为分部类的代码隐藏文件中。</span><span class="sxs-lookup"><span data-stu-id="1957d-486">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="1957d-487">下面的示例演示了默认 `Counter` 组件，该组件在 Blazor 模板生成的应用程序中具有 `@code` 块。</span><span class="sxs-lookup"><span data-stu-id="1957d-487">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="1957d-488">HTML 标记、Razor 代码和C#代码位于同一个文件中：</span><span class="sxs-lookup"><span data-stu-id="1957d-488">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="1957d-489">*Counter*：</span><span class="sxs-lookup"><span data-stu-id="1957d-489">*Counter.razor*:</span></span>

```razor
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

<span data-ttu-id="1957d-490">还可以使用具有分部类的代码隐藏文件创建 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-490">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="1957d-491">*Counter*：</span><span class="sxs-lookup"><span data-stu-id="1957d-491">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="1957d-492">*Counter.razor.cs*：</span><span class="sxs-lookup"><span data-stu-id="1957d-492">*Counter.razor.cs*:</span></span>

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

## <a name="specify-a-component-base-class"></a><span data-ttu-id="1957d-493">指定组件基类</span><span class="sxs-lookup"><span data-stu-id="1957d-493">Specify a component base class</span></span>

<span data-ttu-id="1957d-494">`@inherits` 指令可用于指定组件的基类。</span><span class="sxs-lookup"><span data-stu-id="1957d-494">The `@inherits` directive can be used to specify a base class for a component.</span></span>

<span data-ttu-id="1957d-495">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)演示组件如何继承基类 `BlazorRocksBase`，以提供组件的属性和方法。</span><span class="sxs-lookup"><span data-stu-id="1957d-495">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="1957d-496">*Pages/BlazorRocks*：</span><span class="sxs-lookup"><span data-stu-id="1957d-496">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="1957d-497">*BlazorRocksBase.cs*：</span><span class="sxs-lookup"><span data-stu-id="1957d-497">*BlazorRocksBase.cs*:</span></span>

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

<span data-ttu-id="1957d-498">基类应派生自 `ComponentBase`。</span><span class="sxs-lookup"><span data-stu-id="1957d-498">The base class should derive from `ComponentBase`.</span></span>

::: moniker-end

## <a name="import-components"></a><span data-ttu-id="1957d-499">导入组件</span><span class="sxs-lookup"><span data-stu-id="1957d-499">Import components</span></span>

<span data-ttu-id="1957d-500">使用 Razor 编写的组件的命名空间基于（按优先级顺序）：</span><span class="sxs-lookup"><span data-stu-id="1957d-500">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="1957d-501">Razor 文件（*razor*）标记中的[`@namespace`](xref:mvc/views/razor#namespace)指定（`@namespace BlazorSample.MyNamespace`）。</span><span class="sxs-lookup"><span data-stu-id="1957d-501">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="1957d-502">项目在项目文件中的 `RootNamespace` （`<RootNamespace>BlazorSample</RootNamespace>`）。</span><span class="sxs-lookup"><span data-stu-id="1957d-502">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="1957d-503">项目名称，从项目文件的文件名（ *.csproj*）获取，并从项目根路径到组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-503">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="1957d-504">例如，框架将 *{PROJECT ROOT}/Pages/Index.razor* （*BlazorSample*）解析为命名空间 `BlazorSample.Pages`。</span><span class="sxs-lookup"><span data-stu-id="1957d-504">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="1957d-505">组件遵循C#名称绑定规则。</span><span class="sxs-lookup"><span data-stu-id="1957d-505">Components follow C# name binding rules.</span></span> <span data-ttu-id="1957d-506">对于本示例中的 `Index` 组件，范围内的组件都是组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-506">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="1957d-507">在相同的文件夹中，*页*。</span><span class="sxs-lookup"><span data-stu-id="1957d-507">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="1957d-508">项目的根中未显式指定其他命名空间的组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-508">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="1957d-509">使用 Razor 的[`@using`](xref:mvc/views/razor#using)指令将不同命名空间中定义的组件引入作用域。</span><span class="sxs-lookup"><span data-stu-id="1957d-509">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="1957d-510">如果*BlazorSample/Shared/* 文件夹中存在另一个组件 `NavMenu.razor`，则可以使用以下 `@using` 语句在 `Index.razor` 中使用该组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-510">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="1957d-511">还可以使用其完全限定名称（不需要[`@using`](xref:mvc/views/razor#using)指令）来引用组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-511">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="1957d-512">不支持 `global::` 限定。</span><span class="sxs-lookup"><span data-stu-id="1957d-512">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="1957d-513">不支持导入具有化名 `using` 语句的组件（例如，`@using Foo = Bar`）。</span><span class="sxs-lookup"><span data-stu-id="1957d-513">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="1957d-514">不支持部分限定的名称。</span><span class="sxs-lookup"><span data-stu-id="1957d-514">Partially qualified names aren't supported.</span></span> <span data-ttu-id="1957d-515">例如，不支持添加 `@using BlazorSample` 和引用 `<Shared.NavMenu></Shared.NavMenu>` `NavMenu.razor`。</span><span class="sxs-lookup"><span data-stu-id="1957d-515">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="1957d-516">条件 HTML 元素特性</span><span class="sxs-lookup"><span data-stu-id="1957d-516">Conditional HTML element attributes</span></span>

<span data-ttu-id="1957d-517">HTML 元素特性根据 .NET 值有条件地呈现。</span><span class="sxs-lookup"><span data-stu-id="1957d-517">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="1957d-518">如果值是 `false` 或 `null`，则不会呈现特性。</span><span class="sxs-lookup"><span data-stu-id="1957d-518">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="1957d-519">如果 `true`值，则以最小化的特性呈现特性。</span><span class="sxs-lookup"><span data-stu-id="1957d-519">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="1957d-520">在下面的示例中，`IsCompleted` 确定是否在元素的标记中呈现 `checked`：</span><span class="sxs-lookup"><span data-stu-id="1957d-520">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="1957d-521">如果 `true``IsCompleted`，则复选框将呈现为：</span><span class="sxs-lookup"><span data-stu-id="1957d-521">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="1957d-522">如果 `false``IsCompleted`，则复选框将呈现为：</span><span class="sxs-lookup"><span data-stu-id="1957d-522">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="1957d-523">有关更多信息，请参见<xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="1957d-523">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="1957d-524">当 .NET 类型为 `bool`时，某些 HTML 特性（如[aria 按下](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)）不会正常运行。</span><span class="sxs-lookup"><span data-stu-id="1957d-524">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="1957d-525">在这些情况下，请使用 `string` 类型，而不是 `bool`。</span><span class="sxs-lookup"><span data-stu-id="1957d-525">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="1957d-526">原始 HTML</span><span class="sxs-lookup"><span data-stu-id="1957d-526">Raw HTML</span></span>

<span data-ttu-id="1957d-527">通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文本文本。</span><span class="sxs-lookup"><span data-stu-id="1957d-527">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="1957d-528">若要呈现原始 HTML，请将 HTML 内容换 `MarkupString` 值。</span><span class="sxs-lookup"><span data-stu-id="1957d-528">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="1957d-529">该值将分析为 HTML 或 SVG，并插入 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="1957d-529">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="1957d-530">呈现从任何不受信任的源构造的原始 HTML 会**带来安全风险**，应避免！</span><span class="sxs-lookup"><span data-stu-id="1957d-530">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="1957d-531">下面的示例演示如何使用 `MarkupString` 类型向组件的呈现输出添加静态 HTML 内容块：</span><span class="sxs-lookup"><span data-stu-id="1957d-531">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="1957d-532">模板化组件</span><span class="sxs-lookup"><span data-stu-id="1957d-532">Templated components</span></span>

<span data-ttu-id="1957d-533">模板化组件是接受一个或多个 UI 模板作为参数的组件，可将其用作组件呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="1957d-533">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="1957d-534">模板化组件允许你创作比常规组件更易于使用的更高级别的组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-534">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="1957d-535">几个示例包括：</span><span class="sxs-lookup"><span data-stu-id="1957d-535">A couple of examples include:</span></span>

* <span data-ttu-id="1957d-536">允许用户为表的标头、行和脚注指定模板的表组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-536">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="1957d-537">允许用户在列表中指定用于呈现项的模板的列表组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-537">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="1957d-538">模板参数</span><span class="sxs-lookup"><span data-stu-id="1957d-538">Template parameters</span></span>

<span data-ttu-id="1957d-539">模板化组件通过指定 `RenderFragment` 或 `RenderFragment<T>`类型的一个或多个组件参数进行定义。</span><span class="sxs-lookup"><span data-stu-id="1957d-539">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="1957d-540">呈现片段表示要呈现的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="1957d-540">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="1957d-541">`RenderFragment<T>` 采用可在调用呈现片段时指定的类型参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-541">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="1957d-542">`TableTemplate` 组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-542">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="1957d-543">使用模板化组件时，可以使用与参数名称匹配的子元素（在以下示例中为`TableHeader` 和 `RowTemplate`）指定模板参数：</span><span class="sxs-lookup"><span data-stu-id="1957d-543">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
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

### <a name="template-context-parameters"></a><span data-ttu-id="1957d-544">模板上下文参数</span><span class="sxs-lookup"><span data-stu-id="1957d-544">Template context parameters</span></span>

<span data-ttu-id="1957d-545">作为元素传递的类型 `RenderFragment<T>` 的组件参数具有一个名为 `context` 的隐式参数（例如，前面的代码示例 `@context.PetId`），但你可以使用子元素上的 `Context` 特性来更改参数名称。</span><span class="sxs-lookup"><span data-stu-id="1957d-545">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="1957d-546">在下面的示例中，`RowTemplate` 元素的 `Context` 特性指定了 `pet` 参数：</span><span class="sxs-lookup"><span data-stu-id="1957d-546">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
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

<span data-ttu-id="1957d-547">或者，您可以在 component 元素上指定 `Context` 特性。</span><span class="sxs-lookup"><span data-stu-id="1957d-547">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="1957d-548">指定的 `Context` 特性适用于所有指定的模板参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-548">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="1957d-549">如果要为隐式子内容指定内容参数名称（不包含任何换行子元素），这会很有用。</span><span class="sxs-lookup"><span data-stu-id="1957d-549">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="1957d-550">在下面的示例中，`Context` 特性显示在 `TableTemplate` 元素上，并应用于所有模板参数：</span><span class="sxs-lookup"><span data-stu-id="1957d-550">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
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

### <a name="generic-typed-components"></a><span data-ttu-id="1957d-551">泛型类型化组件</span><span class="sxs-lookup"><span data-stu-id="1957d-551">Generic-typed components</span></span>

<span data-ttu-id="1957d-552">模板化组件通常是通用类型。</span><span class="sxs-lookup"><span data-stu-id="1957d-552">Templated components are often generically typed.</span></span> <span data-ttu-id="1957d-553">例如，泛型 `ListViewTemplate` 组件可用于呈现 `IEnumerable<T>` 值。</span><span class="sxs-lookup"><span data-stu-id="1957d-553">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="1957d-554">若要定义一般组件，请使用[`@typeparam`](xref:mvc/views/razor#typeparam)指令指定类型参数：</span><span class="sxs-lookup"><span data-stu-id="1957d-554">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="1957d-555">当使用泛型类型的组件时，将在可能的情况下推断类型参数：</span><span class="sxs-lookup"><span data-stu-id="1957d-555">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="1957d-556">否则，必须使用与类型参数的名称匹配的属性显式指定 type 参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-556">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="1957d-557">在下面的示例中，`TItem="Pet"` 指定类型：</span><span class="sxs-lookup"><span data-stu-id="1957d-557">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="1957d-558">级联值和参数</span><span class="sxs-lookup"><span data-stu-id="1957d-558">Cascading values and parameters</span></span>

<span data-ttu-id="1957d-559">在某些情况下，使用[组件参数](#component-parameters)将数据从祖先组件流式传输到附属组件是不方便的，尤其是在有多个组件层时。</span><span class="sxs-lookup"><span data-stu-id="1957d-559">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="1957d-560">级联值和参数通过提供一种方便的方法，使上级组件为其所有子代组件提供值。</span><span class="sxs-lookup"><span data-stu-id="1957d-560">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="1957d-561">级联值和参数还提供了一种方法来协调组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-561">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="1957d-562">主题示例</span><span class="sxs-lookup"><span data-stu-id="1957d-562">Theme example</span></span>

<span data-ttu-id="1957d-563">在下面的示例应用程序示例中，`ThemeInfo` 类指定用于向下传递组件层次结构的主题信息，以便应用程序中给定部分内的所有按钮共享相同样式。</span><span class="sxs-lookup"><span data-stu-id="1957d-563">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="1957d-564">*UIThemeClasses/ThemeInfo*：</span><span class="sxs-lookup"><span data-stu-id="1957d-564">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="1957d-565">祖先组件可以使用级联值组件提供级联值。</span><span class="sxs-lookup"><span data-stu-id="1957d-565">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="1957d-566">`CascadingValue` 组件包装组件层次结构的子树，并向该子树内的所有组件提供单个值。</span><span class="sxs-lookup"><span data-stu-id="1957d-566">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="1957d-567">例如，示例应用程序将应用程序布局之一中的主题信息（`ThemeInfo`）指定为组成 `@Body` 属性的布局正文的所有组件的级联参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-567">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="1957d-568">在布局组件中为 `ButtonClass` 分配 `btn-success` 值。</span><span class="sxs-lookup"><span data-stu-id="1957d-568">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="1957d-569">任何子代组件都可以通过 `ThemeInfo` 级联对象使用此属性。</span><span class="sxs-lookup"><span data-stu-id="1957d-569">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="1957d-570">`CascadingValuesParametersLayout` 组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-570">`CascadingValuesParametersLayout` component:</span></span>

```razor
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

<span data-ttu-id="1957d-571">为了使用级联值，组件使用 `[CascadingParameter]` 属性声明级联参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-571">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="1957d-572">级联值按类型绑定到级联参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-572">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="1957d-573">在示例应用中，`CascadingValuesParametersTheme` 组件将 `ThemeInfo` 级联值绑定到级联参数。</span><span class="sxs-lookup"><span data-stu-id="1957d-573">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="1957d-574">参数用于设置由组件显示的一个按钮的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="1957d-574">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="1957d-575">`CascadingValuesParametersTheme` 组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-575">`CascadingValuesParametersTheme` component:</span></span>

```razor
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

<span data-ttu-id="1957d-576">若要在同一子树内级联多个相同类型的值，请为每个 `CascadingValue` 组件及其相应的 `CascadingParameter`提供唯一的 `Name` 字符串。</span><span class="sxs-lookup"><span data-stu-id="1957d-576">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="1957d-577">在下面的示例中，两个 `CascadingValue` 组件按名称级联不同实例 `MyCascadingType`：</span><span class="sxs-lookup"><span data-stu-id="1957d-577">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
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

<span data-ttu-id="1957d-578">在子代组件中，级联参数按名称从上级组件中相应的级联值接收值：</span><span class="sxs-lookup"><span data-stu-id="1957d-578">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="1957d-579">TabSet 示例</span><span class="sxs-lookup"><span data-stu-id="1957d-579">TabSet example</span></span>

<span data-ttu-id="1957d-580">级联参数还允许组件跨组件层次结构进行协作。</span><span class="sxs-lookup"><span data-stu-id="1957d-580">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="1957d-581">例如，请看下面的示例应用中的*TabSet*示例。</span><span class="sxs-lookup"><span data-stu-id="1957d-581">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="1957d-582">该示例应用包含一个选项卡实现的 `ITab` 接口：</span><span class="sxs-lookup"><span data-stu-id="1957d-582">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="1957d-583">`CascadingValuesParametersTabSet` 组件使用 `TabSet` 组件，该组件包含多个 `Tab` 组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-583">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="1957d-584">子 `Tab` 组件不会显式作为参数传递给 `TabSet`。</span><span class="sxs-lookup"><span data-stu-id="1957d-584">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="1957d-585">子 `Tab` 组件是 `TabSet`的子内容的一部分。</span><span class="sxs-lookup"><span data-stu-id="1957d-585">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="1957d-586">但 `TabSet` 仍需要了解每个 `Tab` 组件，以便它可以呈现标头和活动选项卡。若要启用此协调而不需要额外的代码，`TabSet` 组件*可将自身作为级联值提供*，然后由子代 `Tab` 组件选取。</span><span class="sxs-lookup"><span data-stu-id="1957d-586">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="1957d-587">`TabSet` 组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-587">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="1957d-588">子代 `Tab` 组件将包含 `TabSet` 作为级联参数捕获，因此 `Tab` 组件会将其自身添加到 `TabSet` 并协调哪个选项卡处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="1957d-588">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="1957d-589">`Tab` 组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-589">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="1957d-590">Razor 模板</span><span class="sxs-lookup"><span data-stu-id="1957d-590">Razor templates</span></span>

<span data-ttu-id="1957d-591">可以使用 Razor 模板语法来定义呈现片段。</span><span class="sxs-lookup"><span data-stu-id="1957d-591">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="1957d-592">Razor 模板是一种定义 UI 代码段并采用以下格式的一种方法：</span><span class="sxs-lookup"><span data-stu-id="1957d-592">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="1957d-593">下面的示例演示如何在组件中直接指定 `RenderFragment` 和 `RenderFragment<T>` 值以及呈现模板。</span><span class="sxs-lookup"><span data-stu-id="1957d-593">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="1957d-594">呈现片段还可以作为参数传递给[模板化组件](#templated-components)。</span><span class="sxs-lookup"><span data-stu-id="1957d-594">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```razor
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

<span data-ttu-id="1957d-595">前面的代码的呈现输出：</span><span class="sxs-lookup"><span data-stu-id="1957d-595">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="1957d-596">手动 RenderTreeBuilder 逻辑</span><span class="sxs-lookup"><span data-stu-id="1957d-596">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="1957d-597">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` 提供了用于操作组件和元素的方法，包括在代码C#中手动生成组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-597">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="1957d-598">使用 `RenderTreeBuilder` 创建组件是一种高级方案。</span><span class="sxs-lookup"><span data-stu-id="1957d-598">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="1957d-599">格式不正确的组件（例如，未闭合的标记标记）可能会导致未定义的行为。</span><span class="sxs-lookup"><span data-stu-id="1957d-599">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="1957d-600">请考虑以下 `PetDetails` 组件，该组件可以手动内置到另一个组件中：</span><span class="sxs-lookup"><span data-stu-id="1957d-600">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="1957d-601">在下面的示例中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。</span><span class="sxs-lookup"><span data-stu-id="1957d-601">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="1957d-602">当调用 `RenderTreeBuilder` 方法来创建组件（`OpenComponent` 和 `AddAttribute`）时，序列号为源代码行号。</span><span class="sxs-lookup"><span data-stu-id="1957d-602">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="1957d-603">Blazor 差异算法依赖于与不同代码行对应的序列号，而不是不同的调用调用。</span><span class="sxs-lookup"><span data-stu-id="1957d-603">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="1957d-604">使用 `RenderTreeBuilder` 方法创建组件时，将序列号的参数硬编码。</span><span class="sxs-lookup"><span data-stu-id="1957d-604">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="1957d-605">**使用计算或计数器生成序列号会导致性能不佳。**</span><span class="sxs-lookup"><span data-stu-id="1957d-605">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="1957d-606">有关详细信息，请参阅 "[序列号与代码行号和非执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)" 部分。</span><span class="sxs-lookup"><span data-stu-id="1957d-606">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="1957d-607">`BuiltContent` 组件：</span><span class="sxs-lookup"><span data-stu-id="1957d-607">`BuiltContent` component:</span></span>

```razor
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

> [!WARNING]
> <span data-ttu-id="1957d-608">`Microsoft.AspNetCore.Components.RenderTree` 中的类型允许处理呈现操作的*结果*。</span><span class="sxs-lookup"><span data-stu-id="1957d-608">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="1957d-609">这些是 Blazor 框架实现的内部详细信息。</span><span class="sxs-lookup"><span data-stu-id="1957d-609">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="1957d-610">这些类型应被视为不*稳定*，并且在将来的版本中可能会更改。</span><span class="sxs-lookup"><span data-stu-id="1957d-610">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="1957d-611">序列号与代码行号相关，而不是与执行顺序相关</span><span class="sxs-lookup"><span data-stu-id="1957d-611">Sequence numbers relate to code line numbers and not execution order</span></span>

Blazor<span data-ttu-id="1957d-612"> 始终编译 `.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="1957d-612"> `.razor` files are always compiled.</span></span> <span data-ttu-id="1957d-613">这可能对 `.razor` 有很大的优势，因为编译步骤可用于注入在运行时提高应用性能的信息。</span><span class="sxs-lookup"><span data-stu-id="1957d-613">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="1957d-614">这些改进涉及到*序列号*的主要示例。</span><span class="sxs-lookup"><span data-stu-id="1957d-614">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="1957d-615">序列号指示运行时输出来自代码的不同和有序行。</span><span class="sxs-lookup"><span data-stu-id="1957d-615">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="1957d-616">运行时使用此信息以线性时间生成有效的树差异，其速度远快于一般树差异算法通常可以实现的速度。</span><span class="sxs-lookup"><span data-stu-id="1957d-616">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="1957d-617">请考虑以下 Razor 组件（*razor*）文件：</span><span class="sxs-lookup"><span data-stu-id="1957d-617">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="1957d-618">前面的代码编译为类似于下面的内容：</span><span class="sxs-lookup"><span data-stu-id="1957d-618">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="1957d-619">当第一次执行代码时，如果 `true``someFlag`，生成器将接收：</span><span class="sxs-lookup"><span data-stu-id="1957d-619">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="1957d-620">序列</span><span class="sxs-lookup"><span data-stu-id="1957d-620">Sequence</span></span> | <span data-ttu-id="1957d-621">类型</span><span class="sxs-lookup"><span data-stu-id="1957d-621">Type</span></span>      | <span data-ttu-id="1957d-622">数据</span><span class="sxs-lookup"><span data-stu-id="1957d-622">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1957d-623">0</span><span class="sxs-lookup"><span data-stu-id="1957d-623">0</span></span>        | <span data-ttu-id="1957d-624">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1957d-624">Text node</span></span> | <span data-ttu-id="1957d-625">First</span><span class="sxs-lookup"><span data-stu-id="1957d-625">First</span></span>  |
| <span data-ttu-id="1957d-626">1</span><span class="sxs-lookup"><span data-stu-id="1957d-626">1</span></span>        | <span data-ttu-id="1957d-627">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1957d-627">Text node</span></span> | <span data-ttu-id="1957d-628">秒</span><span class="sxs-lookup"><span data-stu-id="1957d-628">Second</span></span> |

<span data-ttu-id="1957d-629">假设 `someFlag` 成为 `false`，并再次呈现标记。</span><span class="sxs-lookup"><span data-stu-id="1957d-629">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="1957d-630">这次，生成器将接收：</span><span class="sxs-lookup"><span data-stu-id="1957d-630">This time, the builder receives:</span></span>

| <span data-ttu-id="1957d-631">序列</span><span class="sxs-lookup"><span data-stu-id="1957d-631">Sequence</span></span> | <span data-ttu-id="1957d-632">类型</span><span class="sxs-lookup"><span data-stu-id="1957d-632">Type</span></span>       | <span data-ttu-id="1957d-633">数据</span><span class="sxs-lookup"><span data-stu-id="1957d-633">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="1957d-634">1</span><span class="sxs-lookup"><span data-stu-id="1957d-634">1</span></span>        | <span data-ttu-id="1957d-635">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1957d-635">Text node</span></span>  | <span data-ttu-id="1957d-636">秒</span><span class="sxs-lookup"><span data-stu-id="1957d-636">Second</span></span> |

<span data-ttu-id="1957d-637">当运行时执行差异时，它会发现序列 `0` 上的项已被删除，因此它生成以下简单的*编辑脚本*：</span><span class="sxs-lookup"><span data-stu-id="1957d-637">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="1957d-638">删除第一个文本节点。</span><span class="sxs-lookup"><span data-stu-id="1957d-638">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="1957d-639">如果以编程方式生成序列号，会出现什么错误</span><span class="sxs-lookup"><span data-stu-id="1957d-639">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="1957d-640">假设你编写了以下呈现树生成器逻辑：</span><span class="sxs-lookup"><span data-stu-id="1957d-640">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="1957d-641">现在，第一个输出是：</span><span class="sxs-lookup"><span data-stu-id="1957d-641">Now, the first output is:</span></span>

| <span data-ttu-id="1957d-642">序列</span><span class="sxs-lookup"><span data-stu-id="1957d-642">Sequence</span></span> | <span data-ttu-id="1957d-643">类型</span><span class="sxs-lookup"><span data-stu-id="1957d-643">Type</span></span>      | <span data-ttu-id="1957d-644">数据</span><span class="sxs-lookup"><span data-stu-id="1957d-644">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1957d-645">0</span><span class="sxs-lookup"><span data-stu-id="1957d-645">0</span></span>        | <span data-ttu-id="1957d-646">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1957d-646">Text node</span></span> | <span data-ttu-id="1957d-647">First</span><span class="sxs-lookup"><span data-stu-id="1957d-647">First</span></span>  |
| <span data-ttu-id="1957d-648">1</span><span class="sxs-lookup"><span data-stu-id="1957d-648">1</span></span>        | <span data-ttu-id="1957d-649">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1957d-649">Text node</span></span> | <span data-ttu-id="1957d-650">秒</span><span class="sxs-lookup"><span data-stu-id="1957d-650">Second</span></span> |

<span data-ttu-id="1957d-651">此结果与以前的情况相同，因此不存在负面问题。</span><span class="sxs-lookup"><span data-stu-id="1957d-651">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="1957d-652">第二个呈现 `false` `someFlag`，输出为：</span><span class="sxs-lookup"><span data-stu-id="1957d-652">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="1957d-653">序列</span><span class="sxs-lookup"><span data-stu-id="1957d-653">Sequence</span></span> | <span data-ttu-id="1957d-654">类型</span><span class="sxs-lookup"><span data-stu-id="1957d-654">Type</span></span>      | <span data-ttu-id="1957d-655">数据</span><span class="sxs-lookup"><span data-stu-id="1957d-655">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="1957d-656">0</span><span class="sxs-lookup"><span data-stu-id="1957d-656">0</span></span>        | <span data-ttu-id="1957d-657">Text 节点</span><span class="sxs-lookup"><span data-stu-id="1957d-657">Text node</span></span> | <span data-ttu-id="1957d-658">秒</span><span class="sxs-lookup"><span data-stu-id="1957d-658">Second</span></span> |

<span data-ttu-id="1957d-659">这次，比较算法会发现*两个*更改已发生，并且算法将生成以下编辑脚本：</span><span class="sxs-lookup"><span data-stu-id="1957d-659">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="1957d-660">将第一个文本节点的值更改为 `Second`。</span><span class="sxs-lookup"><span data-stu-id="1957d-660">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="1957d-661">删除第二个文本节点。</span><span class="sxs-lookup"><span data-stu-id="1957d-661">Remove the second text node.</span></span>

<span data-ttu-id="1957d-662">生成序列号会丢失有关原始代码中 `if/else` 分支和循环的位置的所有有用信息。</span><span class="sxs-lookup"><span data-stu-id="1957d-662">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="1957d-663">这会导致**两次**比较，就像以前一样。</span><span class="sxs-lookup"><span data-stu-id="1957d-663">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="1957d-664">这是一个简单的示例。</span><span class="sxs-lookup"><span data-stu-id="1957d-664">This is a trivial example.</span></span> <span data-ttu-id="1957d-665">在具有复杂、深度嵌套结构的更真实的情况下，特别是在循环中，性能开销更严重。</span><span class="sxs-lookup"><span data-stu-id="1957d-665">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="1957d-666">比较算法不必立即确定哪些循环块或分支已插入或删除，而是必须将其深入地递归到呈现树，并且通常会生成更长的编辑脚本，因为它 misinformed 了新的和新的结构彼此关联。</span><span class="sxs-lookup"><span data-stu-id="1957d-666">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="1957d-667">指导和结论</span><span class="sxs-lookup"><span data-stu-id="1957d-667">Guidance and conclusions</span></span>

* <span data-ttu-id="1957d-668">如果动态生成了序列号，则应用程序性能会受到影响。</span><span class="sxs-lookup"><span data-stu-id="1957d-668">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="1957d-669">框架无法在运行时自动创建自己的序列号，因为所需信息不存在，除非它在编译时捕获。</span><span class="sxs-lookup"><span data-stu-id="1957d-669">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="1957d-670">请勿写入长块手动实现 `RenderTreeBuilder` 逻辑。</span><span class="sxs-lookup"><span data-stu-id="1957d-670">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="1957d-671">首选 `.razor` 文件，并允许编译器处理序列号。</span><span class="sxs-lookup"><span data-stu-id="1957d-671">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="1957d-672">如果无法避免手动 `RenderTreeBuilder` 逻辑，请将长代码块拆分为 `OpenRegion`/`CloseRegion` 调用中的小块。</span><span class="sxs-lookup"><span data-stu-id="1957d-672">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="1957d-673">每个区域都有其自己单独的序列号空间，因此可以从每个区域内的零（或任何其他任意数字）重新启动。</span><span class="sxs-lookup"><span data-stu-id="1957d-673">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="1957d-674">如果对序列号进行硬编码，则 diff 算法只要求序列号的值增加。</span><span class="sxs-lookup"><span data-stu-id="1957d-674">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="1957d-675">初始值和间隙无关。</span><span class="sxs-lookup"><span data-stu-id="1957d-675">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="1957d-676">一个合法选项是将代码行号用作序列号，或从零开始，并按一个或数百个（或任何首选间隔）递增。</span><span class="sxs-lookup"><span data-stu-id="1957d-676">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="1957d-677"> 使用序列号，而其他树比较的 UI 框架不使用序列号。</span><span class="sxs-lookup"><span data-stu-id="1957d-677"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="1957d-678">使用序列号时，比较速度要快得多，Blazor 具有一个编译步骤，该步骤会自动处理序列号，以便为开发人员创作*razor*文件。</span><span class="sxs-lookup"><span data-stu-id="1957d-678">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="localization"></a><span data-ttu-id="1957d-679">Localization</span><span class="sxs-lookup"><span data-stu-id="1957d-679">Localization</span></span>

<span data-ttu-id="1957d-680">使用[本地化中间件](xref:fundamentals/localization#localization-middleware)本地化 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="1957d-680">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="1957d-681">中间件为从应用程序请求资源的用户选择相应的区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-681">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="1957d-682">可以使用以下方法之一设置区域性：</span><span class="sxs-lookup"><span data-stu-id="1957d-682">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="1957d-683">Cookie</span><span class="sxs-lookup"><span data-stu-id="1957d-683">Cookies</span></span>](#cookies)
* [<span data-ttu-id="1957d-684">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="1957d-684">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="1957d-685">有关更多信息和示例，请参见<xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="1957d-685">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="1957d-686">为国际化配置链接器（Blazor WebAssembly）</span><span class="sxs-lookup"><span data-stu-id="1957d-686">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="1957d-687">默认情况下，Blazor 对于 Blazor WebAssembly 应用的链接器配置会去除国际化信息（显式请求的区域设置除外）。</span><span class="sxs-lookup"><span data-stu-id="1957d-687">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="1957d-688">有关控制链接器行为的详细信息和指南，请参阅 <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>。</span><span class="sxs-lookup"><span data-stu-id="1957d-688">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="1957d-689">Cookie</span><span class="sxs-lookup"><span data-stu-id="1957d-689">Cookies</span></span>

<span data-ttu-id="1957d-690">本地化区域性 cookie 可以保存用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-690">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="1957d-691">该 cookie 是由应用程序的主机页（*Pages/host. .cs*）的 `OnGet` 方法创建的。</span><span class="sxs-lookup"><span data-stu-id="1957d-691">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="1957d-692">本地化中间件会在后续请求上读取 cookie，以设置用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-692">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="1957d-693">使用 cookie 可确保 WebSocket 连接可以正确地传播区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-693">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="1957d-694">如果本地化方案基于 URL 路径或查询字符串，则该方案可能无法与 Websocket 一起使用，因此无法持久保存区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-694">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="1957d-695">因此，建议使用本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="1957d-695">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="1957d-696">如果在本地化 cookie 中保留了区域性，则可使用任何方法来分配区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-696">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="1957d-697">如果应用已建立服务器端 ASP.NET Core 的本地化方案，请继续使用应用的现有本地化基础结构，并在应用方案中设置本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="1957d-697">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="1957d-698">下面的示例演示如何在可由本地化中间件读取的 cookie 中设置当前区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-698">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="1957d-699">在 Blazor Server 应用中创建包含以下内容的*页面/主机 .cs*文件：</span><span class="sxs-lookup"><span data-stu-id="1957d-699">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="1957d-700">在应用程序中处理本地化：</span><span class="sxs-lookup"><span data-stu-id="1957d-700">Localization is handled in the app:</span></span>

1. <span data-ttu-id="1957d-701">浏览器将初始 HTTP 请求发送到应用程序。</span><span class="sxs-lookup"><span data-stu-id="1957d-701">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="1957d-702">区域性由本地化中间件分配。</span><span class="sxs-lookup"><span data-stu-id="1957d-702">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="1957d-703">*_Host*中的 `OnGet` 方法将区域性作为响应的一部分保留在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="1957d-703">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="1957d-704">浏览器将打开 WebSocket 连接以创建交互 Blazor 服务器会话。</span><span class="sxs-lookup"><span data-stu-id="1957d-704">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="1957d-705">本地化中间件读取 cookie 并分配区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-705">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="1957d-706">Blazor Server 会话以正确的区域性开头。</span><span class="sxs-lookup"><span data-stu-id="1957d-706">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="1957d-707">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="1957d-707">Provide UI to choose the culture</span></span>

<span data-ttu-id="1957d-708">为了提供允许用户选择区域性的 UI，建议使用*基于重定向的方法*。</span><span class="sxs-lookup"><span data-stu-id="1957d-708">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="1957d-709">此过程类似于用户尝试访问安全资源时在 web 应用中发生的情况，&mdash;用户重定向到登录页，然后重定向回原始资源。</span><span class="sxs-lookup"><span data-stu-id="1957d-709">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="1957d-710">应用程序通过重定向到控制器来持久保存用户的所选区域性。</span><span class="sxs-lookup"><span data-stu-id="1957d-710">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="1957d-711">控制器将用户选定的区域性设置为 cookie，并将用户重定向回原始 URI。</span><span class="sxs-lookup"><span data-stu-id="1957d-711">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="1957d-712">在服务器上创建一个 HTTP 终结点，以在 cookie 中设置用户的所选区域性，并执行重定向回原始 URI：</span><span class="sxs-lookup"><span data-stu-id="1957d-712">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="1957d-713">使用 `LocalRedirect` 操作结果可防止开放重定向攻击。</span><span class="sxs-lookup"><span data-stu-id="1957d-713">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="1957d-714">有关更多信息，请参见<xref:security/preventing-open-redirects>。</span><span class="sxs-lookup"><span data-stu-id="1957d-714">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="1957d-715">以下组件显示了一个示例，说明如何在用户选择区域性时执行初始重定向：</span><span class="sxs-lookup"><span data-stu-id="1957d-715">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
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

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a><span data-ttu-id="1957d-716">在 Blazor 应用中使用 .NET 本地化方案</span><span class="sxs-lookup"><span data-stu-id="1957d-716">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="1957d-717">在 Blazor 应用程序中，可以使用以下 .NET 本地化和全球化方案：</span><span class="sxs-lookup"><span data-stu-id="1957d-717">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="1957d-718">.网络资源系统</span><span class="sxs-lookup"><span data-stu-id="1957d-718">.NET's resources system</span></span>
* <span data-ttu-id="1957d-719">区域性特定的数字和日期格式设置</span><span class="sxs-lookup"><span data-stu-id="1957d-719">Culture-specific number and date formatting</span></span>

Blazor<span data-ttu-id="1957d-720">的 `@bind` 功能基于用户的当前区域性执行全球化。</span><span class="sxs-lookup"><span data-stu-id="1957d-720">'s `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="1957d-721">有关详细信息，请参阅[数据绑定](#data-binding)部分。</span><span class="sxs-lookup"><span data-stu-id="1957d-721">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="1957d-722">目前支持有限的一组 ASP.NET Core 本地化方案：</span><span class="sxs-lookup"><span data-stu-id="1957d-722">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="1957d-723">Blazor 应用*支持*`IStringLocalizer<>`。</span><span class="sxs-lookup"><span data-stu-id="1957d-723">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="1957d-724">`IHtmlLocalizer<>`、`IViewLocalizer<>`和数据批注本地化 ASP.NET Core MVC 方案，但在 Blazor 应用程序中**不受支持**。</span><span class="sxs-lookup"><span data-stu-id="1957d-724">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="1957d-725">有关更多信息，请参见<xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="1957d-725">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="1957d-726">可缩放的向量图形（SVG）图像</span><span class="sxs-lookup"><span data-stu-id="1957d-726">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="1957d-727">由于 Blazor 呈现 HTML，因此支持浏览器支持的图像（包括可缩放的向量图形（SVG）图像（*svg*））是通过 `<img>` 标记支持的：</span><span class="sxs-lookup"><span data-stu-id="1957d-727">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="1957d-728">同样，样式表文件（ *.css*）的 CSS 规则支持 SVG 图像：</span><span class="sxs-lookup"><span data-stu-id="1957d-728">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="1957d-729">但是，在所有情况下均不支持内联 SVG 标记。</span><span class="sxs-lookup"><span data-stu-id="1957d-729">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="1957d-730">如果将 `<svg>` 标记直接放入组件文件（*razor*），则支持基本图像呈现，但尚不支持很多高级方案。</span><span class="sxs-lookup"><span data-stu-id="1957d-730">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="1957d-731">例如，`<use>` 标记当前未遵循，并且不能将 `@bind` 与某些 SVG 标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="1957d-731">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="1957d-732">我们预计会在将来的版本中解决这些限制。</span><span class="sxs-lookup"><span data-stu-id="1957d-732">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1957d-733">其他资源</span><span class="sxs-lookup"><span data-stu-id="1957d-733">Additional resources</span></span>

* <span data-ttu-id="1957d-734"><xref:security/blazor/server> &ndash; 包括构建必须应对资源耗尽的 Blazor 服务器应用的指南。</span><span class="sxs-lookup"><span data-stu-id="1957d-734"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
