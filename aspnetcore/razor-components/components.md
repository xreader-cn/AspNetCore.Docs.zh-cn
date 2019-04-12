---
title: 创建和使用 Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件，包括如何将绑定到数据、 处理事件，以及管理组件生命周期。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/components
ms.openlocfilehash: f8ac7f3844b94a162e8d1c45f80ae153d89536ee
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515437"
---
# <a name="create-and-use-razor-components"></a><span data-ttu-id="7fafe-103">创建和使用 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="7fafe-103">Create and use Razor Components</span></span>

<span data-ttu-id="7fafe-104">通过[Luke Latham](https://github.com/guardrex)， [Daniel Roth](https://github.com/danroth27)，和[Morné Zaayman](https://github.com/MorneZaayman)</span><span class="sxs-lookup"><span data-stu-id="7fafe-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Morné Zaayman](https://github.com/MorneZaayman)</span></span>

<span data-ttu-id="7fafe-105">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="7fafe-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="7fafe-106">有关先决条件，请参阅[入门](xref:razor-components/get-started)主题。</span><span class="sxs-lookup"><span data-stu-id="7fafe-106">See the [Get started](xref:razor-components/get-started) topic for prerequisites.</span></span>

<span data-ttu-id="7fafe-107">使用生成 razor 组件应用*组件*。</span><span class="sxs-lookup"><span data-stu-id="7fafe-107">Razor Components apps are built using *components*.</span></span> <span data-ttu-id="7fafe-108">组件是自包含的块的用户界面 (UI)，如页、 对话框中或窗体。</span><span class="sxs-lookup"><span data-stu-id="7fafe-108">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="7fafe-109">一个组件，包括 HTML 标记和将数据注入或响应 UI 事件所需的处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="7fafe-109">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="7fafe-110">组件是灵活、 轻量。</span><span class="sxs-lookup"><span data-stu-id="7fafe-110">Components are flexible and lightweight.</span></span> <span data-ttu-id="7fafe-111">可以嵌套，重复使用，并在项目间共享。</span><span class="sxs-lookup"><span data-stu-id="7fafe-111">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="7fafe-112">组件类</span><span class="sxs-lookup"><span data-stu-id="7fafe-112">Component classes</span></span>

<span data-ttu-id="7fafe-113">组件通常在 Razor 组件文件中实现 (*.razor*) 组合使用C#和 HTML 标记 (*.cshtml* Blazor 应用中使用文件)。</span><span class="sxs-lookup"><span data-stu-id="7fafe-113">Components are typically implemented in Razor Component files (*.razor*) using a combination of C# and HTML markup (*.cshtml* files are used in Blazor apps).</span></span>

<span data-ttu-id="7fafe-114">可以在 Razor 组件使用的应用程序中编写组件 *.cshtml*文件扩展名，只要这些文件被标识为 Razor 组件文件使用`_RazorComponentInclude`MSBuild 属性。</span><span class="sxs-lookup"><span data-stu-id="7fafe-114">Components can be authored in Razor Components apps using the *.cshtml* file extension as long as the files are identified as Razor Component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="7fafe-115">例如，使用 Razor 组件模板创建的应用指定的所有 *.cshtml*文件下*组件*文件夹应被视为 Razor 组件文件：</span><span class="sxs-lookup"><span data-stu-id="7fafe-115">For example, an app created using the Razor Component template specifies that all *.cshtml* files under the *Components* folder should be treated as Razor Components files:</span></span>

```xml
<_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
```

<span data-ttu-id="7fafe-116">使用 HTML 定义组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="7fafe-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="7fafe-117">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="7fafe-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="7fafe-118">当编译 Razor 组件的应用程序、 HTML 标记和C#呈现逻辑转换为 component 类。</span><span class="sxs-lookup"><span data-stu-id="7fafe-118">When a Razor Components app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="7fafe-119">生成的类的名称匹配的文件的名称。</span><span class="sxs-lookup"><span data-stu-id="7fafe-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="7fafe-120">在中定义的 component 类成员`@functions`块 (多个`@functions`块是允许)。</span><span class="sxs-lookup"><span data-stu-id="7fafe-120">Members of the component class are defined in a `@functions` block (more than one `@functions` block is permissible).</span></span> <span data-ttu-id="7fafe-121">在`@functions`块中，组件状态 （属性、 字段） 指定与事件处理或定义其他组件的逻辑的方法。</span><span class="sxs-lookup"><span data-stu-id="7fafe-121">In the `@functions` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span>

<span data-ttu-id="7fafe-122">组件成员然后，可以使用组件的一部分的呈现逻辑使用如C#表达式的开头`@`。</span><span class="sxs-lookup"><span data-stu-id="7fafe-122">Component members can then be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="7fafe-123">例如，C#通过前缀来呈现字段`@`对字段名称。</span><span class="sxs-lookup"><span data-stu-id="7fafe-123">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="7fafe-124">下面的示例的计算结果并呈现：</span><span class="sxs-lookup"><span data-stu-id="7fafe-124">The following example evaluates and renders:</span></span>

* `_headingFontStyle` <span data-ttu-id="7fafe-125">为的 CSS 属性值`font-style`。</span><span class="sxs-lookup"><span data-stu-id="7fafe-125">to the CSS property value for `font-style`.</span></span>
* `_headingText` <span data-ttu-id="7fafe-126">内容`<h1>`元素。</span><span class="sxs-lookup"><span data-stu-id="7fafe-126">to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="7fafe-127">最初呈现该组件后，将组件重新生成其呈现树以响应事件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-127">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="7fafe-128">Razor 组件然后将对上一个新的呈现树进行比较，并应用到浏览器的文档对象模型 (DOM) 的任何修改。</span><span class="sxs-lookup"><span data-stu-id="7fafe-128">Razor Components then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="7fafe-129">将组件集成到 Razor 页面和 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="7fafe-129">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="7fafe-130">使用与现有的 Razor 页面和 MVC 应用程序组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-130">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="7fafe-131">没有无需重写现有页面或视图，以使用 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-131">There's no need to rewrite existing pages or views to use Razor Components.</span></span> <span data-ttu-id="7fafe-132">呈现的页面或视图时，组件是预呈现&dagger;在同一时间。</span><span class="sxs-lookup"><span data-stu-id="7fafe-132">When the page or view is rendered, components are prerendered&dagger; at the same time.</span></span> 

> [!NOTE]
> <span data-ttu-id="7fafe-133">&dagger;默认情况下，服务器端预呈现启用 Razor 组件应用。</span><span class="sxs-lookup"><span data-stu-id="7fafe-133">&dagger;Server-side prerendering is enabled for Razor Components apps by default.</span></span> <span data-ttu-id="7fafe-134">客户端 Blazor 应用将在即将发布的预览版 4 版本中支持预呈现。</span><span class="sxs-lookup"><span data-stu-id="7fafe-134">Client-side Blazor apps will support prerendering in the upcoming Preview 4 release.</span></span> <span data-ttu-id="7fafe-135">有关详细信息，请参阅[更新模板/中间件使用 MapFallbackToPage/文件](https://github.com/aspnet/AspNetCore/issues/8852)。</span><span class="sxs-lookup"><span data-stu-id="7fafe-135">For more information, see [Update templates/middleware to use MapFallbackToPage/File](https://github.com/aspnet/AspNetCore/issues/8852).</span></span>

<span data-ttu-id="7fafe-136">若要呈现的页面或视图中的组件，使用`RenderComponentAsync<TComponent>`HTML 帮助器方法：</span><span class="sxs-lookup"><span data-stu-id="7fafe-136">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="7fafe-137">呈现页面和视图组件尚不可在预览版 3 发布交互式。</span><span class="sxs-lookup"><span data-stu-id="7fafe-137">Components rendered from pages and views aren't yet interactive in the Preview 3 release.</span></span> <span data-ttu-id="7fafe-138">例如，选择一个按钮不会触发方法调用。</span><span class="sxs-lookup"><span data-stu-id="7fafe-138">For example, selecting a button doesn't trigger a method call.</span></span> <span data-ttu-id="7fafe-139">将来的预览版将解除此限制，并添加对呈现组件使用正常的元素和属性语法的支持。</span><span class="sxs-lookup"><span data-stu-id="7fafe-139">A future preview will address this limitation and add support for rendering components using the normal element and attribute syntax.</span></span>

<span data-ttu-id="7fafe-140">虽然页面和视图可以使用组件，相反说法不正确。</span><span class="sxs-lookup"><span data-stu-id="7fafe-140">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="7fafe-141">组件不能使用视图和页面特定方案，如分部视图和部分。</span><span class="sxs-lookup"><span data-stu-id="7fafe-141">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="7fafe-142">若要使用到组件的逻辑从组件中的分部视图、 分部视图逻辑的身份。</span><span class="sxs-lookup"><span data-stu-id="7fafe-142">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

## <a name="using-components"></a><span data-ttu-id="7fafe-143">使用组件</span><span class="sxs-lookup"><span data-stu-id="7fafe-143">Using components</span></span>

<span data-ttu-id="7fafe-144">组件可以通过声明它们中包括其他组件使用 HTML 元素的语法。</span><span class="sxs-lookup"><span data-stu-id="7fafe-144">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="7fafe-145">使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。</span><span class="sxs-lookup"><span data-stu-id="7fafe-145">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="7fafe-146">以下标记呈现`HeadingComponent`(*HeadingComponent.cshtml*) 实例：</span><span class="sxs-lookup"><span data-stu-id="7fafe-146">The following markup renders a `HeadingComponent` (*HeadingComponent.cshtml*) instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.cshtml?name=snippet_HeadingComponent)]

## <a name="component-parameters"></a><span data-ttu-id="7fafe-147">组件参数</span><span class="sxs-lookup"><span data-stu-id="7fafe-147">Component parameters</span></span>

<span data-ttu-id="7fafe-148">组件可以具有*组件参数*，这使用定义*非公共*使用修饰组件类上的属性， `[Parameter]`。</span><span class="sxs-lookup"><span data-stu-id="7fafe-148">Components can have *component parameters*, which are defined using *non-public* properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="7fafe-149">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="7fafe-149">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="7fafe-150">在以下示例中，`ParentComponent`设置的值`Title`属性的`ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="7fafe-150">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`:</span></span>

<span data-ttu-id="7fafe-151">*ParentComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-151">*ParentComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=5)]

<span data-ttu-id="7fafe-152">*ChildComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-152">*ChildComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=7-8)]

## <a name="child-content"></a><span data-ttu-id="7fafe-153">子内容</span><span class="sxs-lookup"><span data-stu-id="7fafe-153">Child content</span></span>

<span data-ttu-id="7fafe-154">组件可以设置另一个组件的内容。</span><span class="sxs-lookup"><span data-stu-id="7fafe-154">Components can set the content of another component.</span></span> <span data-ttu-id="7fafe-155">分配的组件提供标记，用于指定接收组件之间的内容。</span><span class="sxs-lookup"><span data-stu-id="7fafe-155">The assigning component provides the content between the tags that specify the receiving component.</span></span> <span data-ttu-id="7fafe-156">例如，`ParentComponent`可以提供内容呈现子组件上来将中的内容`<ChildComponent>`标记。</span><span class="sxs-lookup"><span data-stu-id="7fafe-156">For example, a `ParentComponent` can provide content for rendering by a Child component by placing the content inside `<ChildComponent>` tags.</span></span>

<span data-ttu-id="7fafe-157">*ParentComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-157">*ParentComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=6-7)]

<span data-ttu-id="7fafe-158">子组件具有`ChildContent`属性表示`RenderFragment`。</span><span class="sxs-lookup"><span data-stu-id="7fafe-158">The Child component has a `ChildContent` property that represents a `RenderFragment`.</span></span> <span data-ttu-id="7fafe-159">值`ChildContent`定位子组件的标记中来呈现内容的位置。</span><span class="sxs-lookup"><span data-stu-id="7fafe-159">The value of `ChildContent` is positioned in the child component's markup where the content should be rendered.</span></span> <span data-ttu-id="7fafe-160">在下面的示例中，值`ChildContent`收到来自父组件和 Bootstrap 面板内呈现`panel-body`。</span><span class="sxs-lookup"><span data-stu-id="7fafe-160">In the following example, the value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="7fafe-161">*ChildComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-161">*ChildComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=3,10-11)]

> [!NOTE]
> <span data-ttu-id="7fafe-162">属性接收`RenderFragment`内容必须命名为`ChildContent`按照约定。</span><span class="sxs-lookup"><span data-stu-id="7fafe-162">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

## <a name="data-binding"></a><span data-ttu-id="7fafe-163">数据绑定</span><span class="sxs-lookup"><span data-stu-id="7fafe-163">Data binding</span></span>

<span data-ttu-id="7fafe-164">可以使用数据绑定到组件和 DOM 元素来完成`bind`属性。</span><span class="sxs-lookup"><span data-stu-id="7fafe-164">Data binding to both components and DOM elements is accomplished with the `bind` attribute.</span></span> <span data-ttu-id="7fafe-165">以下示例将绑定`ItalicsCheck`属性设置为复选框的选中状态：</span><span class="sxs-lookup"><span data-stu-id="7fafe-165">The following example binds the `ItalicsCheck` property to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    bind="@_italicsCheck">
```

<span data-ttu-id="7fafe-166">如果复选框被选中，然后清除，该属性的值将更新为`true`和`false`分别。</span><span class="sxs-lookup"><span data-stu-id="7fafe-166">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="7fafe-167">仅当呈现该组件时，不在响应不断变化的属性的值时，将在 UI 中更新复选框。</span><span class="sxs-lookup"><span data-stu-id="7fafe-167">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="7fafe-168">由于组件自身的呈现事件处理程序代码执行后，属性更新是通常立即反映在用户界面。</span><span class="sxs-lookup"><span data-stu-id="7fafe-168">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="7fafe-169">使用`bind`与`CurrentValue`属性 (`<input bind="@CurrentValue">`) 实质上等同于以下：</span><span class="sxs-lookup"><span data-stu-id="7fafe-169">Using `bind` with a `CurrentValue` property (`<input bind="@CurrentValue">`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)">
```

<span data-ttu-id="7fafe-170">当呈现该组件时，`value`输入元素的来自`CurrentValue`属性。</span><span class="sxs-lookup"><span data-stu-id="7fafe-170">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="7fafe-171">当用户在文本框中，键入`onchange`触发事件和`CurrentValue`属性设置为已更改的值。</span><span class="sxs-lookup"><span data-stu-id="7fafe-171">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="7fafe-172">在实际情况下，代码生成是一种较为复杂，因为`bind`处理少数情况下，将执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="7fafe-172">In reality, the code generation is a little more complex because `bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="7fafe-173">原则上，`bind`将具有的表达式的当前值相关联`value`使用已注册处理程序的属性和处理更改。</span><span class="sxs-lookup"><span data-stu-id="7fafe-173">In principle, `bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="7fafe-174">除了`onchange`，可以使用其他事件，例如绑定的属性`oninput`更清楚地了解要将绑定到内容的方式：</span><span class="sxs-lookup"><span data-stu-id="7fafe-174">In addition to `onchange`, the property can be bound using other events like `oninput` by being more explicit about what to bind to:</span></span>

```cshtml
<input type="text" bind-value-oninput="@CurrentValue">
```

<span data-ttu-id="7fafe-175">与不同`onchange`，`oninput`文本框中输入的每个字符时激发。</span><span class="sxs-lookup"><span data-stu-id="7fafe-175">Unlike `onchange`, `oninput` fires for every character that is input into the text box.</span></span>

**<span data-ttu-id="7fafe-176">格式字符串</span><span class="sxs-lookup"><span data-stu-id="7fafe-176">Format strings</span></span>**

<span data-ttu-id="7fafe-177">数据绑定的工作方式与<xref:System.DateTime>格式字符串。</span><span class="sxs-lookup"><span data-stu-id="7fafe-177">Data binding works with <xref:System.DateTime> format strings.</span></span> <span data-ttu-id="7fafe-178">其他格式的表达式，如货币或数字格式，此时不可用。</span><span class="sxs-lookup"><span data-stu-id="7fafe-178">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd">

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="7fafe-179">`format-value`属性指定要应用到的日期格式`value`的`input`元素。</span><span class="sxs-lookup"><span data-stu-id="7fafe-179">The `format-value` attribute specifies the date format to apply to the `value` of the `input` element.</span></span> <span data-ttu-id="7fafe-180">此外使用的格式将值解析时`onchange`事件发生。</span><span class="sxs-lookup"><span data-stu-id="7fafe-180">The format is also used to parse the value when an `onchange` event occurs.</span></span>

**<span data-ttu-id="7fafe-181">组件参数</span><span class="sxs-lookup"><span data-stu-id="7fafe-181">Component parameters</span></span>**

<span data-ttu-id="7fafe-182">绑定还可识别组件参数，其中`bind-{property}`可以跨组件绑定的属性值。</span><span class="sxs-lookup"><span data-stu-id="7fafe-182">Binding also recognizes component parameters, where `bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="7fafe-183">以下组件使用`ChildComponent`，并将绑定`ParentYear`参数是从父级到`Year`的子组件的参数：</span><span class="sxs-lookup"><span data-stu-id="7fafe-183">The following component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

<span data-ttu-id="7fafe-184">父组件：</span><span class="sxs-lookup"><span data-stu-id="7fafe-184">Parent component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent bind-Year="@ParentYear" />

<button class="btn btn-primary" onclick="@ChangeTheYear">
    Change Year to 1986
</button>

@functions {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="7fafe-185">子组件：</span><span class="sxs-lookup"><span data-stu-id="7fafe-185">Child component:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@functions {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private Action<int> YearChanged { get; set; }
}
```

<span data-ttu-id="7fafe-186">正在加载`ParentComponent`生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="7fafe-186">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="7fafe-187">如果的值`ParentYear`通过选择中的按钮更改属性`ParentComponent`，则`Year`属性的`ChildComponent`更新。</span><span class="sxs-lookup"><span data-stu-id="7fafe-187">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="7fafe-188">新值`Year`在 UI 中显示时`ParentComponent`重新呈现：</span><span class="sxs-lookup"><span data-stu-id="7fafe-188">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="7fafe-189">`Year`参数是可绑定，因为它具有一个辅助性`YearChanged`相匹配的类型的事件`Year`参数。</span><span class="sxs-lookup"><span data-stu-id="7fafe-189">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="7fafe-190">按照约定，`<ChildComponent bind-Year="@ParentYear" />`实质上等同于写入操作，</span><span class="sxs-lookup"><span data-stu-id="7fafe-190">By convention, `<ChildComponent bind-Year="@ParentYear" />` is essentially equivalent to writing,</span></span>

```cshtml
    <ChildComponent bind-Year-YearChanged="@ParentYear" />
```

<span data-ttu-id="7fafe-191">一般情况下，将属性绑定到相应的事件处理程序使用`bind-property-event`属性。</span><span class="sxs-lookup"><span data-stu-id="7fafe-191">In general, a property can be bound to a corresponding event handler using `bind-property-event` attribute.</span></span>

## <a name="event-handling"></a><span data-ttu-id="7fafe-192">事件处理</span><span class="sxs-lookup"><span data-stu-id="7fafe-192">Event handling</span></span>

<span data-ttu-id="7fafe-193">Razor 组件提供的事件处理功能。</span><span class="sxs-lookup"><span data-stu-id="7fafe-193">Razor Components provide event handling features.</span></span> <span data-ttu-id="7fafe-194">HTML 元素特性名为`on<event>`(例如， `onclick`， `onsubmit`) 与委托类型的值，Razor 组件属性的值将视为一个事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="7fafe-194">For an HTML element attribute named `on<event>` (for example, `onclick`, `onsubmit`) with a delegate-typed value, Razor Components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="7fafe-195">该属性的名称始终以开头`on`。</span><span class="sxs-lookup"><span data-stu-id="7fafe-195">The attribute's name always starts with `on`.</span></span>

<span data-ttu-id="7fafe-196">下面的代码调用`UpdateHeading`方法在 UI 中选择该按钮时：</span><span class="sxs-lookup"><span data-stu-id="7fafe-196">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="7fafe-197">下面的代码调用`CheckboxChanged`方法时在 UI 中更改该复选框：</span><span class="sxs-lookup"><span data-stu-id="7fafe-197">The following code calls the `CheckboxChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged">

@functions {
    private void CheckboxChanged()
    {
        ...
    }
}
```

<span data-ttu-id="7fafe-198">事件处理程序也可以是异步和返回<xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="7fafe-198">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="7fafe-199">无需手动调用`StateHasChanged()`。</span><span class="sxs-lookup"><span data-stu-id="7fafe-199">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="7fafe-200">发生时，会记录异常。</span><span class="sxs-lookup"><span data-stu-id="7fafe-200">Exceptions are logged when they occur.</span></span>

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="7fafe-201">对于某些事件，允许使用特定于事件的事件参数类型。</span><span class="sxs-lookup"><span data-stu-id="7fafe-201">For some events, event-specific event argument types are permitted.</span></span> <span data-ttu-id="7fafe-202">如果对其中一种事件类型的访问不必要的它无需在方法调用中。</span><span class="sxs-lookup"><span data-stu-id="7fafe-202">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="7fafe-203">支持的事件自变量的列表是：</span><span class="sxs-lookup"><span data-stu-id="7fafe-203">The list of supported event arguments is:</span></span>

* <span data-ttu-id="7fafe-204">UIEventArgs</span><span class="sxs-lookup"><span data-stu-id="7fafe-204">UIEventArgs</span></span>
* <span data-ttu-id="7fafe-205">UIChangeEventArgs</span><span class="sxs-lookup"><span data-stu-id="7fafe-205">UIChangeEventArgs</span></span>
* <span data-ttu-id="7fafe-206">UIKeyboardEventArgs</span><span class="sxs-lookup"><span data-stu-id="7fafe-206">UIKeyboardEventArgs</span></span>
* <span data-ttu-id="7fafe-207">UIMouseEventArgs</span><span class="sxs-lookup"><span data-stu-id="7fafe-207">UIMouseEventArgs</span></span>

<span data-ttu-id="7fafe-208">此外可以使用 lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="7fafe-208">Lambda expressions can also be used:</span></span>

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="7fafe-209">通常很方便地通过其他值，如关闭时循环访问一组元素。</span><span class="sxs-lookup"><span data-stu-id="7fafe-209">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="7fafe-210">下面的示例创建三个按钮的它调用的每个`UpdateHeading`传递的事件自变量 (`UIMouseEventArgs`)，以及它按钮编号 (`buttonNumber`) 时在 UI 中选择：</span><span class="sxs-lookup"><span data-stu-id="7fafe-210">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@functions {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            "mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="7fafe-211">不要**不**使用循环变量 (`i`) 中`for`直接在 lambda 表达式中的循环。</span><span class="sxs-lookup"><span data-stu-id="7fafe-211">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="7fafe-212">否则相同的变量可供所有 lambda 表达式导致`i`要在所有 lambda 是相同的值。</span><span class="sxs-lookup"><span data-stu-id="7fafe-212">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="7fafe-213">始终捕获本地变量中的其值 (`buttonNumber`在前面的示例)，然后使用它。</span><span class="sxs-lookup"><span data-stu-id="7fafe-213">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="7fafe-214">捕获对组件的引用</span><span class="sxs-lookup"><span data-stu-id="7fafe-214">Capture references to components</span></span>

<span data-ttu-id="7fafe-215">组件的引用提供的方式获取对组件实例的引用，以便您可以发出命令到该实例，如`Show`或`Reset`。</span><span class="sxs-lookup"><span data-stu-id="7fafe-215">Component references provide a way get a reference to a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="7fafe-216">若要捕获的组件引用，请添加`ref`属性到子组件，然后将具有相同名称和相同类型的字段定义为子组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-216">To capture a component reference, add a `ref` attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

```cshtml
<MyLoginDialog ref="loginDialog" ... />

@functions {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="7fafe-217">当呈现该组件时，`loginDialog`字段填充`MyLoginDialog`子组件实例。</span><span class="sxs-lookup"><span data-stu-id="7fafe-217">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="7fafe-218">然后可以调用组件实例上的.NET 方法。</span><span class="sxs-lookup"><span data-stu-id="7fafe-218">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7fafe-219">`loginDialog`呈现该组件并包括其输出后，仅填充变量`MyLoginDialog`元素。</span><span class="sxs-lookup"><span data-stu-id="7fafe-219">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="7fafe-220">该点，直到没有要引用。</span><span class="sxs-lookup"><span data-stu-id="7fafe-220">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="7fafe-221">若要操作组件的引用，该组件已经完成呈现后，使用`OnAfterRenderAsync`或`OnAfterRender`方法。</span><span class="sxs-lookup"><span data-stu-id="7fafe-221">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="7fafe-222">而捕获组件引用使用到类似的语法[捕获元素引用](xref:razor-components/javascript-interop#capture-references-to-elements)，它不是[JavaScript 互操作](xref:razor-components/javascript-interop)功能。</span><span class="sxs-lookup"><span data-stu-id="7fafe-222">While capturing component references uses a similar syntax to [capturing element references](xref:razor-components/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:razor-components/javascript-interop) feature.</span></span> <span data-ttu-id="7fafe-223">组件的引用不会传递给 JavaScript 代码;它们仅用于.NET 代码中。</span><span class="sxs-lookup"><span data-stu-id="7fafe-223">Component references aren't passed to JavaScript code; they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="7fafe-224">不要**不**组件引用用于改变子组件的状态。</span><span class="sxs-lookup"><span data-stu-id="7fafe-224">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="7fafe-225">相反，使用普通的声明性参数将数据传递给子组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-225">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="7fafe-226">这将导致子组件来自动 rerender 在正确的时间。</span><span class="sxs-lookup"><span data-stu-id="7fafe-226">This causes child components to rerender at the correct times automatically.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="7fafe-227">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="7fafe-227">Lifecycle methods</span></span>

`OnInitAsync` <span data-ttu-id="7fafe-228">和`OnInit`执行代码以初始化该组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-228">and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="7fafe-229">若要执行异步操作，请使用`OnInitAsync`和`await`关键字的操作：</span><span class="sxs-lookup"><span data-stu-id="7fafe-229">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="7fafe-230">为同步操作，使用`OnInit`:</span><span class="sxs-lookup"><span data-stu-id="7fafe-230">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync` <span data-ttu-id="7fafe-231">和`OnParametersSet`组件已收到来自其父级的参数和值分配给属性时会调用。</span><span class="sxs-lookup"><span data-stu-id="7fafe-231">and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="7fafe-232">组件初始化后执行这些方法，然后每次呈现该组件是：</span><span class="sxs-lookup"><span data-stu-id="7fafe-232">These methods are executed after component initialization and then each time the component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync` <span data-ttu-id="7fafe-233">和`OnAfterRender`组件已完成呈现后调用。</span><span class="sxs-lookup"><span data-stu-id="7fafe-233">and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="7fafe-234">此时即会填充元素和组件的引用。</span><span class="sxs-lookup"><span data-stu-id="7fafe-234">Element and component references are populated at this point.</span></span> <span data-ttu-id="7fafe-235">使用此阶段执行附加的初始化步骤中使用的呈现的内容，如激活对呈现的 DOM 元素的第三方 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="7fafe-235">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

`SetParameters` <span data-ttu-id="7fafe-236">可以重写以执行代码之前设置参数：</span><span class="sxs-lookup"><span data-stu-id="7fafe-236">can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="7fafe-237">如果`base.SetParameters`不是调用，自定义代码可以解释中任何方式所需的传入参数值。</span><span class="sxs-lookup"><span data-stu-id="7fafe-237">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="7fafe-238">例如，传入参数无需分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="7fafe-238">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

`ShouldRender` <span data-ttu-id="7fafe-239">可以重写以禁止显示 UI 的刷新。</span><span class="sxs-lookup"><span data-stu-id="7fafe-239">can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="7fafe-240">如果该实现返回`true`，刷新用户界面。</span><span class="sxs-lookup"><span data-stu-id="7fafe-240">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="7fafe-241">即使`ShouldRender`是被重写时，务必使最初呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-241">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="7fafe-242">使用 IDisposable 组件可供使用</span><span class="sxs-lookup"><span data-stu-id="7fafe-242">Component disposal with IDisposable</span></span>

<span data-ttu-id="7fafe-243">如果组件实现<xref:System.IDisposable>，则[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)从 UI 中移除该组件时调用。</span><span class="sxs-lookup"><span data-stu-id="7fafe-243">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="7fafe-244">以下组件使用`@implements IDisposable`和`Dispose`方法：</span><span class="sxs-lookup"><span data-stu-id="7fafe-244">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@functions {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a><span data-ttu-id="7fafe-245">路由</span><span class="sxs-lookup"><span data-stu-id="7fafe-245">Routing</span></span>

<span data-ttu-id="7fafe-246">Razor 组件中的路由是通过提供对应用程序中每个可访问组件的路由模板实现的。</span><span class="sxs-lookup"><span data-stu-id="7fafe-246">Routing in Razor Components is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="7fafe-247">当 *.cshtml*文件具有`@page`编译指令，则生成的类提供<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>指定路由模板。</span><span class="sxs-lookup"><span data-stu-id="7fafe-247">When a *.cshtml* file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="7fafe-248">在运行时，路由器将查看与组件类`RouteAttribute`并呈现任何组件有匹配的请求的 URL 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="7fafe-248">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="7fafe-249">多个路由模板可以应用于一个组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-249">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="7fafe-250">以下组件响应的请求`/BlazorRoute`和`/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="7fafe-250">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="7fafe-251">路由参数</span><span class="sxs-lookup"><span data-stu-id="7fafe-251">Route parameters</span></span>

<span data-ttu-id="7fafe-252">组件可以接收来自路由模板中提供的路由参数`@page`指令。</span><span class="sxs-lookup"><span data-stu-id="7fafe-252">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="7fafe-253">路由器使用路由参数来填充相应的组件参数。</span><span class="sxs-lookup"><span data-stu-id="7fafe-253">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="7fafe-254">*RouteParameter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-254">*RouteParameter.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter)]

<span data-ttu-id="7fafe-255">不支持可选参数，因此两个`@page`指令收取在上面的示例。</span><span class="sxs-lookup"><span data-stu-id="7fafe-255">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="7fafe-256">第一个可以导航到不带参数的组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-256">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="7fafe-257">第二个`@page`指令采用`{text}`路由参数和值赋给`Text`属性。</span><span class="sxs-lookup"><span data-stu-id="7fafe-257">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="7fafe-258">"代码隐藏"体验的基类继承</span><span class="sxs-lookup"><span data-stu-id="7fafe-258">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="7fafe-259">组件文件 (*.cshtml*) 混合使用 HTML 标记和C#处理同一文件中的代码。</span><span class="sxs-lookup"><span data-stu-id="7fafe-259">Component files (*.cshtml*) mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="7fafe-260">`@inherits`指令可用于向 Razor 组件应用提供一种"代码隐藏"体验，将组件标记与处理代码分隔开来。</span><span class="sxs-lookup"><span data-stu-id="7fafe-260">The `@inherits` directive can be used to provide Razor Components apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="7fafe-261">[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/)显示了如何一个组件可以继承一个基类， `BlazorRocksBase`，以提供该组件的属性和方法。</span><span class="sxs-lookup"><span data-stu-id="7fafe-261">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="7fafe-262">*BlazorRocks.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-262">*BlazorRocks.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.cshtml?name=snippet_BlazorRocks)]

<span data-ttu-id="7fafe-263">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-263">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="7fafe-264">应派生自的基类`ComponentBase`。</span><span class="sxs-lookup"><span data-stu-id="7fafe-264">The base class should derive from `ComponentBase`.</span></span>

## <a name="razor-support"></a><span data-ttu-id="7fafe-265">Razor 支持</span><span class="sxs-lookup"><span data-stu-id="7fafe-265">Razor support</span></span>

**<span data-ttu-id="7fafe-266">Razor 指令</span><span class="sxs-lookup"><span data-stu-id="7fafe-266">Razor directives</span></span>**

<span data-ttu-id="7fafe-267">Razor 指令表所示。</span><span class="sxs-lookup"><span data-stu-id="7fafe-267">Razor directives are shown in the following table.</span></span>

| <span data-ttu-id="7fafe-268">指令</span><span class="sxs-lookup"><span data-stu-id="7fafe-268">Directive</span></span> | <span data-ttu-id="7fafe-269">描述</span><span class="sxs-lookup"><span data-stu-id="7fafe-269">Description</span></span> |
| --------- | ----------- |
| [<span data-ttu-id="7fafe-270">\@函数</span><span class="sxs-lookup"><span data-stu-id="7fafe-270">\@functions</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="7fafe-271">添加C#到组件的代码块。</span><span class="sxs-lookup"><span data-stu-id="7fafe-271">Adds a C# code block to a component.</span></span> |
| `@implements` | <span data-ttu-id="7fafe-272">实现生成的组件类的接口。</span><span class="sxs-lookup"><span data-stu-id="7fafe-272">Implements an interface for the generated component class.</span></span> |
| [<span data-ttu-id="7fafe-273">\@继承</span><span class="sxs-lookup"><span data-stu-id="7fafe-273">\@inherits</span></span>](xref:mvc/views/razor#section-3) | <span data-ttu-id="7fafe-274">提供该组件继承的类的完全控制。</span><span class="sxs-lookup"><span data-stu-id="7fafe-274">Provides full control of the class that the component inherits.</span></span> |
| [<span data-ttu-id="7fafe-275">\@inject</span><span class="sxs-lookup"><span data-stu-id="7fafe-275">\@inject</span></span>](xref:mvc/views/razor#section-4) | <span data-ttu-id="7fafe-276">允许服务从注入[服务容器](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="7fafe-276">Enables service injection from the [service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7fafe-277">有关详细信息，请参阅[视图中的依赖关系注入](xref:mvc/views/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="7fafe-277">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span> |
| `@layout` | <span data-ttu-id="7fafe-278">指定布局组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-278">Specifies a layout component.</span></span> <span data-ttu-id="7fafe-279">布局组件用于避免代码重复和不一致。</span><span class="sxs-lookup"><span data-stu-id="7fafe-279">Layout components are used to avoid code duplication and inconsistency.</span></span> |
| [<span data-ttu-id="7fafe-280">\@page</span><span class="sxs-lookup"><span data-stu-id="7fafe-280">\@page</span></span>](xref:razor-pages/index#razor-pages) | <span data-ttu-id="7fafe-281">指定该组件应直接处理请求。</span><span class="sxs-lookup"><span data-stu-id="7fafe-281">Specifies that the component should handle requests directly.</span></span> <span data-ttu-id="7fafe-282">`@page`指令可指定路由参数和可选参数。</span><span class="sxs-lookup"><span data-stu-id="7fafe-282">The `@page` directive can be specified with a route and optional parameters.</span></span> <span data-ttu-id="7fafe-283">Razor 页面与`@page`指令不需要是在文件顶部的第一个指令。</span><span class="sxs-lookup"><span data-stu-id="7fafe-283">Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file.</span></span> <span data-ttu-id="7fafe-284">有关详细信息，请参阅[路由](xref:razor-components/routing)。</span><span class="sxs-lookup"><span data-stu-id="7fafe-284">For more information, see [Routing](xref:razor-components/routing).</span></span> |
| [<span data-ttu-id="7fafe-285">\@使用</span><span class="sxs-lookup"><span data-stu-id="7fafe-285">\@using</span></span>](xref:mvc/views/razor#using) | <span data-ttu-id="7fafe-286">添加C#`using`指令生成的组件类。</span><span class="sxs-lookup"><span data-stu-id="7fafe-286">Adds the C# `using` directive to the generated component class.</span></span> |
| [<span data-ttu-id="7fafe-287">\@addTagHelper</span><span class="sxs-lookup"><span data-stu-id="7fafe-287">\@addTagHelper</span></span>](xref:mvc/views/razor#tag-helpers) | <span data-ttu-id="7fafe-288">使用`@addTagHelper`比应用程序的程序集的不同程序集中使用的组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-288">Use `@addTagHelper` to use a component in a different assembly than the app's assembly.</span></span> |

**<span data-ttu-id="7fafe-289">条件属性</span><span class="sxs-lookup"><span data-stu-id="7fafe-289">Conditional attributes</span></span>**

<span data-ttu-id="7fafe-290">基于.NET 值有条件地呈现属性。</span><span class="sxs-lookup"><span data-stu-id="7fafe-290">Attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="7fafe-291">如果值为`false`或`null`，该属性不会呈现。</span><span class="sxs-lookup"><span data-stu-id="7fafe-291">If the value is `false` or `null`,  the attribute isn't rendered.</span></span> <span data-ttu-id="7fafe-292">如果值为`true`，呈现该属性最小化。</span><span class="sxs-lookup"><span data-stu-id="7fafe-292">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="7fafe-293">在以下示例中，`IsCompleted`确定如果`checked`呈现控件的标记中：</span><span class="sxs-lookup"><span data-stu-id="7fafe-293">In the following example, `IsCompleted` determines if `checked` is rendered in the control's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted">

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="7fafe-294">如果`IsCompleted`是`true`，复选框将呈现为：</span><span class="sxs-lookup"><span data-stu-id="7fafe-294">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked>
```

<span data-ttu-id="7fafe-295">如果`IsCompleted`是`false`，复选框将呈现为：</span><span class="sxs-lookup"><span data-stu-id="7fafe-295">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox">
```

**<span data-ttu-id="7fafe-296">Razor 的其他信息</span><span class="sxs-lookup"><span data-stu-id="7fafe-296">Additional information on Razor</span></span>**

<span data-ttu-id="7fafe-297">Razor 的详细信息，请参阅[Razor 语法参考](xref:mvc/views/razor)。</span><span class="sxs-lookup"><span data-stu-id="7fafe-297">For more information on Razor, see the [Razor syntax reference](xref:mvc/views/razor).</span></span>

## <a name="raw-html"></a><span data-ttu-id="7fafe-298">原始 HTML</span><span class="sxs-lookup"><span data-stu-id="7fafe-298">Raw HTML</span></span>

<span data-ttu-id="7fafe-299">字符串通常使用呈现文本的 DOM 节点，这意味着它们可能包含任何标记被忽略，视为文字文本。</span><span class="sxs-lookup"><span data-stu-id="7fafe-299">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="7fafe-300">若要呈现原始 HTML，包装中的 HTML 内容`MarkupString`值。</span><span class="sxs-lookup"><span data-stu-id="7fafe-300">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="7fafe-301">分析为 HTML 或 SVG 值并将其插入到 dom。</span><span class="sxs-lookup"><span data-stu-id="7fafe-301">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="7fafe-302">呈现的原始 HTML 构造从任何不受信任的源是**安全风险**，应避免使用 ！</span><span class="sxs-lookup"><span data-stu-id="7fafe-302">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="7fafe-303">下面的示例演示使用`MarkupString`要静态 HTML 内容的块添加到呈现的输出的组件类型：</span><span class="sxs-lookup"><span data-stu-id="7fafe-303">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@functions {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="7fafe-304">模板化组件</span><span class="sxs-lookup"><span data-stu-id="7fafe-304">Templated components</span></span>

<span data-ttu-id="7fafe-305">模板化组件是接受一个或多个 UI 模板作为参数，然后可以使用的组件的呈现逻辑一部分的组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-305">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="7fafe-306">模板化组件允许您编写更高级别的更常规的组件可重用的组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-306">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="7fafe-307">几个示例包括：</span><span class="sxs-lookup"><span data-stu-id="7fafe-307">A couple of examples include:</span></span>

* <span data-ttu-id="7fafe-308">允许用户指定的表的标头、 行和页脚模板的表组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-308">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="7fafe-309">允许用户在列表中指定用于呈现项的模板的列表组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-309">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="7fafe-310">模板参数</span><span class="sxs-lookup"><span data-stu-id="7fafe-310">Template parameters</span></span>

<span data-ttu-id="7fafe-311">通过指定类型的一个或多个组件参数来定义模板化组件`RenderFragment`或`RenderFragment<T>`。</span><span class="sxs-lookup"><span data-stu-id="7fafe-311">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="7fafe-312">呈现片段表示 UI 呈现组件的一个段。</span><span class="sxs-lookup"><span data-stu-id="7fafe-312">A render fragment represents a segment of UI that is rendered by the component.</span></span> <span data-ttu-id="7fafe-313">呈现片段 （可选） 使用调用呈现段时可以指定一个参数。</span><span class="sxs-lookup"><span data-stu-id="7fafe-313">A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="7fafe-314">*Components/TableTemplate.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-314">*Components/TableTemplate.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.cshtml)]

<span data-ttu-id="7fafe-315">在使用模板化组件时，可以使用与匹配的参数的名称的子元素指定模板参数 (`TableHeader`和`RowTemplate`在下面的示例):</span><span class="sxs-lookup"><span data-stu-id="7fafe-315">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="@pets">
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

### <a name="template-context-parameters"></a><span data-ttu-id="7fafe-316">模板上下文参数</span><span class="sxs-lookup"><span data-stu-id="7fafe-316">Template context parameters</span></span>

<span data-ttu-id="7fafe-317">组件类型的自变量`RenderFragment<T>`元素具有名为的隐式参数传递`context`(例如，从前面的代码示例中， `@context.PetId`)，但您可以更改参数名称使用`Context`在子活动的属性元素。</span><span class="sxs-lookup"><span data-stu-id="7fafe-317">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="7fafe-318">在以下示例中，`RowTemplate`元素的`Context`特性指定`pet`参数：</span><span class="sxs-lookup"><span data-stu-id="7fafe-318">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="@pets">
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

<span data-ttu-id="7fafe-319">或者，可以指定`Context`组件元素的特性。</span><span class="sxs-lookup"><span data-stu-id="7fafe-319">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="7fafe-320">指定`Context`特性应用于所有指定的模板参数。</span><span class="sxs-lookup"><span data-stu-id="7fafe-320">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="7fafe-321">当你想要指定隐式子内容的内容的参数名称时这很有用 (而无需任何包装子元素)。</span><span class="sxs-lookup"><span data-stu-id="7fafe-321">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="7fafe-322">在以下示例中，`Context`属性将会显示在`TableTemplate`元素并将应用于所有模板参数：</span><span class="sxs-lookup"><span data-stu-id="7fafe-322">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="@pets" Context="pet">
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

### <a name="generic-typed-components"></a><span data-ttu-id="7fafe-323">泛型类型的组件</span><span class="sxs-lookup"><span data-stu-id="7fafe-323">Generic-typed components</span></span>

<span data-ttu-id="7fafe-324">模板化组件通常以一般方式类型化。</span><span class="sxs-lookup"><span data-stu-id="7fafe-324">Templated components are often generically typed.</span></span> <span data-ttu-id="7fafe-325">例如，泛型列表视图模板组件可用于呈现`IEnumerable<T>`值。</span><span class="sxs-lookup"><span data-stu-id="7fafe-325">For example, a generic List View Template component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="7fafe-326">若要定义的通用组件，请使用`@typeparam`指令，用于指定类型参数。</span><span class="sxs-lookup"><span data-stu-id="7fafe-326">To define a generic component, use the `@typeparam` directive to specify type parameters.</span></span>

<span data-ttu-id="7fafe-327">*Components/ListViewTemplate.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-327">*Components/ListViewTemplate.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.cshtml)]

<span data-ttu-id="7fafe-328">当使用泛型类型的组件，如有可能推断类型参数：</span><span class="sxs-lookup"><span data-stu-id="7fafe-328">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="7fafe-329">否则，类型参数必须显式指定使用匹配的类型参数名称的属性。</span><span class="sxs-lookup"><span data-stu-id="7fafe-329">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="7fafe-330">在以下示例中，`TItem="Pet"`指定的类型：</span><span class="sxs-lookup"><span data-stu-id="7fafe-330">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="7fafe-331">级联值和参数</span><span class="sxs-lookup"><span data-stu-id="7fafe-331">Cascading values and parameters</span></span>

<span data-ttu-id="7fafe-332">在某些情况下，很不方便将数据流从下级组件使用的祖先组件[组件参数](#component-parameters)，尤其是当存在多个组件层。</span><span class="sxs-lookup"><span data-stu-id="7fafe-332">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="7fafe-333">级联值和参数的上级组件提供到其所有下级组件的值的简便方法，从而解决此问题。</span><span class="sxs-lookup"><span data-stu-id="7fafe-333">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="7fafe-334">级联值和参数还提供组件进行协调的方法。</span><span class="sxs-lookup"><span data-stu-id="7fafe-334">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="7fafe-335">主题的示例</span><span class="sxs-lookup"><span data-stu-id="7fafe-335">Theme example</span></span>

<span data-ttu-id="7fafe-336">在下面的示例*主题*示例应用中，从示例`ThemeInfo`类指定要沿组件层次结构向下传递，以便在应用程序的给定部分中的按钮的所有共享相同的样式的主题信息。</span><span class="sxs-lookup"><span data-stu-id="7fafe-336">In the following *Theme* example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="7fafe-337">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-337">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="7fafe-338">上级组件可以提供使用级联值组件的级联值。</span><span class="sxs-lookup"><span data-stu-id="7fafe-338">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="7fafe-339">级联值组件包装的组件层次结构的子树，并提供对该树内的所有组件的单个值。</span><span class="sxs-lookup"><span data-stu-id="7fafe-339">The Cascading Value component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="7fafe-340">例如，示例应用程序指定的主题信息 (`ThemeInfo`) 中的应用的布局的所有组件构成的布局部分的级联参数作为一个`@Body`属性。</span><span class="sxs-lookup"><span data-stu-id="7fafe-340">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> `ButtonClass` <span data-ttu-id="7fafe-341">分配的值`btn-success`布局组件中。</span><span class="sxs-lookup"><span data-stu-id="7fafe-341">is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="7fafe-342">任何下级组件可以使用此属性通过`ThemeInfo`级联的对象。</span><span class="sxs-lookup"><span data-stu-id="7fafe-342">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="7fafe-343">*Shared/CascadingValuesParametersLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-343">*Shared/CascadingValuesParametersLayout.cshtml*:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@functions {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="7fafe-344">若要使使用级联值中，组件声明使用级联参数`[CascadingParameter]`属性，或基于字符串名称值：</span><span class="sxs-lookup"><span data-stu-id="7fafe-344">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

<span data-ttu-id="7fafe-345">如果具有相同类型的多个级联值并且需要区分相同的子树中，使用字符串名称值的绑定是相关。</span><span class="sxs-lookup"><span data-stu-id="7fafe-345">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="7fafe-346">级联值绑定的类型是为级联参数。</span><span class="sxs-lookup"><span data-stu-id="7fafe-346">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="7fafe-347">在示例应用中，级联值参数主题组件绑定到`ThemeInfo`级联级联参数值。</span><span class="sxs-lookup"><span data-stu-id="7fafe-347">In the sample app, the Cascading Values Parameters Theme component binds to the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="7fafe-348">参数用于设置用于的一个由组件显示按钮的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="7fafe-348">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="7fafe-349">*Pages/CascadingValuesParametersTheme.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-349">*Pages/CascadingValuesParametersTheme.cshtml*:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" onclick="@IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" onclick="@IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@functions {
    private int currentCount = 0;

    [CascadingParameter] protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a><span data-ttu-id="7fafe-350">选项卡设置示例</span><span class="sxs-lookup"><span data-stu-id="7fafe-350">TabSet example</span></span>

<span data-ttu-id="7fafe-351">级联参数还启用协作组件层次结构间的组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-351">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="7fafe-352">例如，请考虑以下*选项卡设置*示例应用程序中的示例。</span><span class="sxs-lookup"><span data-stu-id="7fafe-352">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="7fafe-353">示例应用有`ITab`选项卡实现的接口：</span><span class="sxs-lookup"><span data-stu-id="7fafe-353">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="7fafe-354">级联值参数选项卡设置组件使用选项卡上设置的组件，其中包含多个选项卡组件：</span><span class="sxs-lookup"><span data-stu-id="7fafe-354">The Cascading Values Parameters TabSet component uses the Tab Set component, which contains several Tab components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.cshtml?name=snippet_TabSet)]

<span data-ttu-id="7fafe-355">子选项卡组件不是显式作为参数传递到选项卡上设置。</span><span class="sxs-lookup"><span data-stu-id="7fafe-355">The child Tab components aren't explicitly passed as parameters to the Tab Set.</span></span> <span data-ttu-id="7fafe-356">相反，子选项卡组件是子内容的选项卡上设置的一部分。</span><span class="sxs-lookup"><span data-stu-id="7fafe-356">Instead, the child Tab components are part of the child content of the Tab Set.</span></span> <span data-ttu-id="7fafe-357">但是，选项卡上设置仍需要了解有关每个选项卡组件，以便它可以呈现标头和活动选项卡。若要启用此协调，而无需其他代码，该选项卡上设置的组件*可以提供自身的级联值作为*，然后拾取后代的选项卡组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-357">However, the Tab Set still needs to know about each Tab component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the Tab Set component *can provide itself as a cascading value* that is then picked up by the descendent Tab components.</span></span>

<span data-ttu-id="7fafe-358">*Components/TabSet.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-358">*Components/TabSet.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.cshtml)]

<span data-ttu-id="7fafe-359">子选项卡组件捕获包含选项卡集作为一个级联参数，因此选项卡组件将自己添加到选项卡上设置和坐标的选项卡上处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="7fafe-359">The descendent Tab components capture the containing Tab Set as a cascading parameter, so the Tab components add themselves to the Tab Set and coordinate on which tab is active.</span></span>

<span data-ttu-id="7fafe-360">*Components/Tab.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-360">*Components/Tab.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.cshtml)]

## <a name="razor-templates"></a><span data-ttu-id="7fafe-361">Razor 模板</span><span class="sxs-lookup"><span data-stu-id="7fafe-361">Razor templates</span></span>

<span data-ttu-id="7fafe-362">呈现可使用 Razor 模板语法定义片段。</span><span class="sxs-lookup"><span data-stu-id="7fafe-362">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="7fafe-363">Razor 模板是一种方法来定义 UI 代码段，并采用以下格式：</span><span class="sxs-lookup"><span data-stu-id="7fafe-363">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="7fafe-364">下面的示例演示如何指定`RenderFragment`和`RenderFragment<T>`值。</span><span class="sxs-lookup"><span data-stu-id="7fafe-364">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values.</span></span>

<span data-ttu-id="7fafe-365">*RazorTemplates.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7fafe-365">*RazorTemplates.cshtml*:</span></span>

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

<span data-ttu-id="7fafe-366">呈现使用的 Razor 模板可以作为参数传递给模板化组件或直接呈现定义的片段。</span><span class="sxs-lookup"><span data-stu-id="7fafe-366">Render fragments defined using Razor templates can be passed as arguments to templated components or rendered directly.</span></span> <span data-ttu-id="7fafe-367">例如，使用以下 Razor 标记直接呈现上一个模板：</span><span class="sxs-lookup"><span data-stu-id="7fafe-367">For example, the previous templates are directly rendered with the following Razor markup:</span></span>

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

<span data-ttu-id="7fafe-368">呈现的输出：</span><span class="sxs-lookup"><span data-stu-id="7fafe-368">Rendered output:</span></span>

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="7fafe-369">手动 RenderTreeBuilder 逻辑</span><span class="sxs-lookup"><span data-stu-id="7fafe-369">Manual RenderTreeBuilder logic</span></span>

`Microsoft.AspNetCore.Components.RenderTree` <span data-ttu-id="7fafe-370">提供用于操作组件和元素，其中包括中手动生成组件的方法C#代码。</span><span class="sxs-lookup"><span data-stu-id="7fafe-370">provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="7fafe-371">使用`RenderTreeBuilder`创建组件是一个高级的方案。</span><span class="sxs-lookup"><span data-stu-id="7fafe-371">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="7fafe-372">格式不正确的组件 （例如，未关闭的标记） 可能会导致未定义的行为。</span><span class="sxs-lookup"><span data-stu-id="7fafe-372">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="7fafe-373">请考虑以下宠物的详细信息组件 (*PetDetails.razor* Razor 组件; 中*PetDetails.cshtml* Blazor 中)，这可以手动构建到另一个组件：</span><span class="sxs-lookup"><span data-stu-id="7fafe-373">Consider the following Pet Details component (*PetDetails.razor* in Razor Components; *PetDetails.cshtml* in Blazor), which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote<p>

@functions
{
    [Parameter]
    string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="7fafe-374">在下面的示例中的循环`CreateComponent`方法将生成三个宠物的详细信息组件。</span><span class="sxs-lookup"><span data-stu-id="7fafe-374">In the following example, the loop in the `CreateComponent` method generates three Pet Details components.</span></span> <span data-ttu-id="7fafe-375">调用时`RenderTreeBuilder`方法创建的组件 (`OpenComponent`和`AddAttribute`)，序列号是源代码行号。</span><span class="sxs-lookup"><span data-stu-id="7fafe-375">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="7fafe-376">差异算法依赖于与非重复行代码，没有不同之处相对应的序列号 Razor 组件调用调用。</span><span class="sxs-lookup"><span data-stu-id="7fafe-376">The Razor Components difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="7fafe-377">创建具有组件时`RenderTreeBuilder`方法、 进行硬编码的序列号的参数。</span><span class="sxs-lookup"><span data-stu-id="7fafe-377">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> **<span data-ttu-id="7fafe-378">使用计算或计数器生成的序列号可能会导致性能不佳。</span><span class="sxs-lookup"><span data-stu-id="7fafe-378">Using a calculation or counter to generate the sequence number can lead to poor performance.</span></span>**

<span data-ttu-id="7fafe-379">构建的组件 (*BuiltContent.razor* Razor 组件; 中*BuiltContent.cshtml* Blazor 中):</span><span class="sxs-lookup"><span data-stu-id="7fafe-379">Built component (*BuiltContent.razor* in Razor Components; *BuiltContent.cshtml* in Blazor):</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" onclick="@RenderComponent">
    Create three Pet Details components
</button>

@functions {
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
