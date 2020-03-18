---
title: ASP.NET Core Blazor 数据绑定
author: guardrex
description: 了解适用于 Blazor 应用中的组件和 DOM 元素的数据绑定方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: 92377730b9d353a507ffd384710fb979affe7265
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648222"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="a6ce2-103">ASP.NET Core Blazor 数据绑定</span><span class="sxs-lookup"><span data-stu-id="a6ce2-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="a6ce2-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a6ce2-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="a6ce2-105">针对组件和 DOM 元素的数据绑定通过 [`@bind`](xref:mvc/views/razor#bind) 属性实现。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-105">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="a6ce2-106">下面的示例将 `CurrentValue` 属性绑定到文本框的值：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-106">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="a6ce2-107">当文本框失去焦点时，该属性的值会更新。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="a6ce2-108">仅当呈现组件时（而不是响应属性值更改），才会在 UI 中更新文本框。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="a6ce2-109">由于组件会在事件处理程序代码执行之后呈现自己，因此在触发事件处理程序之后，通常会  在 UI 中反映属性更新。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="a6ce2-110">将 `@bind` 与 `CurrentValue` 属性结合使用 (`<input @bind="CurrentValue" />`) 在本质上等效于以下内容：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="a6ce2-111">呈现组件时，输入元素的 `value` 来自 `CurrentValue` 属性。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="a6ce2-112">用户在文本框中键入并更改元素焦点时，会激发 `onchange` 事件并将 `CurrentValue` 属性设置为更改的值。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="a6ce2-113">实际上，代码生成更加复杂，因为 `@bind` 会处理执行类型转换的情况。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="a6ce2-114">原则上，`@bind` 将表达式的当前值与 `value` 属性关联，并使用注册的处理程序处理更改。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="a6ce2-115">通过同时包含带有 `event` 参数的 `@bind:event` 属性，在其他事件上绑定属性或字段。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="a6ce2-116">以下示例在 `oninput` 事件上绑定 `CurrentValue` 属性：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="a6ce2-117">与在元素失去焦点时激发的 `onchange` 不同，`oninput` 在文本框的值更改时激发。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="a6ce2-118">通过 `@bind-{ATTRIBUTE}:event` 语法使用 `@bind-{ATTRIBUTE}` 可绑定除 `value` 之外的元素属性。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="a6ce2-119">在下面的示例中，当 `_paragraphStyle` 值更改时，段落的样式会更新：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

## <a name="unparsable-values"></a><span data-ttu-id="a6ce2-120">无法分析的值</span><span class="sxs-lookup"><span data-stu-id="a6ce2-120">Unparsable values</span></span>

<span data-ttu-id="a6ce2-121">如果用户向数据绑定元素提供无法分析的值，则在触发绑定事件时，无法分析的值会自动还原为以前的值。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-121">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="a6ce2-122">请参考以下方案：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-122">Consider the following scenario:</span></span>

* <span data-ttu-id="a6ce2-123">`<input>` 元素绑定到 `int` 类型，其初始值为 `123`：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-123">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="a6ce2-124">用户在页面中将该元素的值更新为 `123.45`，并更改元素焦点。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-124">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="a6ce2-125">在上面的方案中，元素的值会还原为 `123`。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-125">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="a6ce2-126">如果拒绝值 `123.45` 以采用原始值 `123`，则用户会了解其值不被接受。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-126">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="a6ce2-127">默认情况下，绑定适用于元素的 `onchange` 事件 (`@bind="{PROPERTY OR FIELD}"`)。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-127">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="a6ce2-128">使用 `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` 对其他事件触发绑定。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-128">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="a6ce2-129">对于 `oninput` 事件 (`@bind:event="oninput"`)，在任何引入无法分析的值的击键之后，会进行还原。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-129">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="a6ce2-130">当使用 `int` 绑定类型以 `oninput` 事件为目标时，会阻止用户键入 `.` 字符。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-130">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="a6ce2-131">`.` 字符会立即删除，因此用户会收到仅允许整数的即时反馈。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-131">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="a6ce2-132">在某些情况下，在 `oninput` 事件中还原值并不理想，例如在应该允许用户清除无法解析的 `<input>` 值时。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-132">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="a6ce2-133">替代方案包括：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-133">Alternatives include:</span></span>

* <span data-ttu-id="a6ce2-134">不使用 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-134">Don't use the `oninput` event.</span></span> <span data-ttu-id="a6ce2-135">使用默认 `onchange` 事件（仅指定 `@bind="{PROPERTY OR FIELD}"`），其中无效值在元素失去焦点之前不会还原。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-135">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="a6ce2-136">绑定到可以为 null 的类型（如 `int?` 或 `string`），并提供自定义逻辑来处理无效条目。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-136">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="a6ce2-137">使用[窗体验证组件](xref:blazor/forms-validation)，如 `InputNumber` 或 `InputDate`。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-137">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="a6ce2-138">窗体验证组件具有用于管理无效输入的内置支持。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-138">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="a6ce2-139">窗体验证组件：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-139">Form validation components:</span></span>
  * <span data-ttu-id="a6ce2-140">允许用户提供无效输入并在关联的 `EditContext` 上接收验证错误。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-140">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="a6ce2-141">在 UI 中显示验证错误，而不干扰用户输入其他 webform 数据。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-141">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="a6ce2-142">格式字符串</span><span class="sxs-lookup"><span data-stu-id="a6ce2-142">Format strings</span></span>

<span data-ttu-id="a6ce2-143">数据绑定使用 [`@bind:format`](xref:mvc/views/razor#bind) 处理 <xref:System.DateTime> 格式字符串。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-143">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="a6ce2-144">目前无法使用其他格式表达式，如货币或数字格式。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-144">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="a6ce2-145">在前面的代码中，`<input>` 元素的字段类型 (`type`) 默认为 `text`。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-145">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="a6ce2-146">对于绑定以下 .NET 类型，支持 `@bind:format`：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-146">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="a6ce2-147"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="a6ce2-147"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="a6ce2-148"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="a6ce2-148"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="a6ce2-149">`@bind:format` 属性指定要应用于 `<input>` 元素的 `value` 的日期格式。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-149">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="a6ce2-150">该格式还用于在 `onchange` 事件发生时分析值。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-150">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="a6ce2-151">不建议为 `date` 字段类型指定格式，因为 Blazor 具有用于设置日期格式的内置支持。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-151">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="a6ce2-152">尽管提出了建议，但如果使用 `date` 字段类型提供格式，则只有使用 `yyyy-MM-dd` 日期格式才能使绑定正常工作：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-152">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="a6ce2-153">使用组件参数的父级到子级绑定</span><span class="sxs-lookup"><span data-stu-id="a6ce2-153">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="a6ce2-154">绑定可识别组件参数，其中 `@bind-{PROPERTY}` 可以将属性值从父组件向下绑定到子组件。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-154">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="a6ce2-155">在[使用链接绑定的子级到父级绑定](#child-to-parent-binding-with-chained-bind)部分中，介绍了从子级绑定到父级。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-155">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="a6ce2-156">以下子组件 (`ChildComponent`) 具有 `Year` 组件参数和 `YearChanged` 回调：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-156">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="a6ce2-157">`EventCallback<T>` 在 <xref:blazor/event-handling#eventcallback> 中进行了说明。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-157">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="a6ce2-158">以下父组件使用：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-158">The following parent component uses:</span></span>

* <span data-ttu-id="a6ce2-159">`ChildComponent` 并将 `ParentYear` 参数从父级绑定到子组件上的 `Year` 参数。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-159">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="a6ce2-160">`onclick` 事件用于触发 `ChangeTheYear` 方法。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-160">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="a6ce2-161">有关详细信息，请参阅 <xref:blazor/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-161">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="a6ce2-162">加载 `ParentComponent` 会生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-162">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="a6ce2-163">如果通过在 `ParentComponent` 中选择按钮来更改 `ParentYear` 属性的值，则会更新 `ChildComponent` 的 `Year` 属性。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-163">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="a6ce2-164">当重新呈现 `ParentComponent` 时，`Year` 的新值会呈现在 UI 中：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-164">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="a6ce2-165">`Year` 参数是可绑定的，因为它具有与 `Year` 参数类型相匹配的伴随 `YearChanged` 事件。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-165">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="a6ce2-166">按照约定，`<ChildComponent @bind-Year="ParentYear" />` 在本质上等效于编写：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-166">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="a6ce2-167">通常，可以通过包含 `@bind-{PROPRETY}:event` 特性将属性绑定到对应的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-167">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="a6ce2-168">例如，可以使用以下两个特性将属性 `MyProp` 绑定到 `MyEventHandler`：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-168">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="a6ce2-169">使用链接绑定的子级到父级绑定</span><span class="sxs-lookup"><span data-stu-id="a6ce2-169">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="a6ce2-170">一种常见方案是将数据绑定参数链接到组件输出中的页面元素。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-170">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="a6ce2-171">此方案称为链接绑定  ，因为多个级别的绑定会同时进行。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-171">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="a6ce2-172">无法在页面元素中使用 `@bind` 语法实现链接绑定。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-172">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="a6ce2-173">必须单独指定事件处理程序和值。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-173">The event handler and value must be specified separately.</span></span> <span data-ttu-id="a6ce2-174">但是，父组件可以将 `@bind` 语法用于组件的参数。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-174">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="a6ce2-175">以下 `PasswordField` 组件 (PasswordField.razor  )：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-175">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="a6ce2-176">将 `<input>` 元素的值设置为 `Password` 属性。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-176">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="a6ce2-177">使用 [EventCallback](xref:blazor/event-handling#eventcallback) 向父组件公开 `Password` 属性的更改。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-177">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="a6ce2-178">使用 `onclick` 事件触发 `ToggleShowPassword` 方法。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-178">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="a6ce2-179">有关详细信息，请参阅 <xref:blazor/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-179">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h2>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

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
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="a6ce2-180">`PasswordField` 组件在另一个组件中使用：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-180">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="a6ce2-181">对前面示例中的密码执行检查或捕获错误：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-181">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="a6ce2-182">为 `Password` 创建支持字段（在下面的示例代码中为 `_password`）。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-182">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="a6ce2-183">在 `Password` 资源库中执行检查或捕获错误。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-183">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="a6ce2-184">如果密码的值中使用了空格，则以下示例会向用户提供即时反馈：</span><span class="sxs-lookup"><span data-stu-id="a6ce2-184">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
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
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="a6ce2-185">单选按钮</span><span class="sxs-lookup"><span data-stu-id="a6ce2-185">Radio buttons</span></span>

<span data-ttu-id="a6ce2-186">有关绑定到窗体中的单选按钮的信息，请参阅 <xref:blazor/forms-validation#work-with-radio-buttons>。</span><span class="sxs-lookup"><span data-stu-id="a6ce2-186">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
