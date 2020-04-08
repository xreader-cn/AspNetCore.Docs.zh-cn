---
title: ASP.NET Core Blazor 数据绑定
author: guardrex
description: 了解 Blazor 应用中组件和 DOM 元素的数据绑定功能。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320951"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="1e045-103">ASP.NET Core Blazor 数据绑定</span><span class="sxs-lookup"><span data-stu-id="1e045-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="1e045-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1e045-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1e045-105">Razor 组件通过名为 [`@bind`](xref:mvc/views/razor#bind) 的 HTML 元素特性提供了数据绑定功能，该特性具有字段、属性或 Razor 表达式值。</span><span class="sxs-lookup"><span data-stu-id="1e045-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="1e045-106">下面的示例将 `CurrentValue` 属性绑定到文本框的值：</span><span class="sxs-lookup"><span data-stu-id="1e045-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1e045-107">当文本框失去焦点时，该属性的值会更新。</span><span class="sxs-lookup"><span data-stu-id="1e045-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="1e045-108">仅当呈现组件时（而不是响应属性值更改），才会在 UI 中更新文本框。</span><span class="sxs-lookup"><span data-stu-id="1e045-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="1e045-109">由于组件会在事件处理程序代码执行之后呈现自己，因此在触发事件处理程序之后，通常会  在 UI 中反映属性更新。</span><span class="sxs-lookup"><span data-stu-id="1e045-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="1e045-110">将 `@bind` 与 `CurrentValue` 属性结合使用 (`<input @bind="CurrentValue" />`) 在本质上等效于以下内容：</span><span class="sxs-lookup"><span data-stu-id="1e045-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1e045-111">呈现组件时，输入元素的 `value` 来自 `CurrentValue` 属性。</span><span class="sxs-lookup"><span data-stu-id="1e045-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="1e045-112">用户在文本框中键入并更改元素焦点时，会激发 `onchange` 事件并将 `CurrentValue` 属性设置为更改的值。</span><span class="sxs-lookup"><span data-stu-id="1e045-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="1e045-113">实际上，代码生成更加复杂，因为 `@bind` 会处理执行类型转换的情况。</span><span class="sxs-lookup"><span data-stu-id="1e045-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="1e045-114">原则上，`@bind` 将表达式的当前值与 `value` 属性关联，并使用注册的处理程序处理更改。</span><span class="sxs-lookup"><span data-stu-id="1e045-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="1e045-115">通过同时包含带有 `@bind:event` 参数的 `event` 属性，在其他事件上绑定属性或字段。</span><span class="sxs-lookup"><span data-stu-id="1e045-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="1e045-116">以下示例在 `CurrentValue` 事件上绑定 `oninput` 属性：</span><span class="sxs-lookup"><span data-stu-id="1e045-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1e045-117">与在元素失去焦点时激发的 `onchange` 不同，`oninput` 在文本框的值更改时激发。</span><span class="sxs-lookup"><span data-stu-id="1e045-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="1e045-118">通过 `@bind-{ATTRIBUTE}` 语法使用 `@bind-{ATTRIBUTE}:event` 可绑定除 `value` 之外的元素属性。</span><span class="sxs-lookup"><span data-stu-id="1e045-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="1e045-119">在下面的示例中，当 `_paragraphStyle` 值更改时，段落的样式会更新：</span><span class="sxs-lookup"><span data-stu-id="1e045-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

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

<span data-ttu-id="1e045-120">属性绑定是区分大小写的。</span><span class="sxs-lookup"><span data-stu-id="1e045-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="1e045-121">例如，`@bind` 有效，而 `@Bind` 无效。</span><span class="sxs-lookup"><span data-stu-id="1e045-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="1e045-122">无法分析的值</span><span class="sxs-lookup"><span data-stu-id="1e045-122">Unparsable values</span></span>

<span data-ttu-id="1e045-123">如果用户向数据绑定元素提供无法分析的值，则在触发绑定事件时，无法分析的值会自动还原为以前的值。</span><span class="sxs-lookup"><span data-stu-id="1e045-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="1e045-124">请参考以下方案：</span><span class="sxs-lookup"><span data-stu-id="1e045-124">Consider the following scenario:</span></span>

* <span data-ttu-id="1e045-125">`<input>` 元素绑定到 `int` 类型，其初始值为 `123`：</span><span class="sxs-lookup"><span data-stu-id="1e045-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="1e045-126">用户在页面中将该元素的值更新为 `123.45`，并更改元素焦点。</span><span class="sxs-lookup"><span data-stu-id="1e045-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="1e045-127">在上面的方案中，元素的值会还原为 `123`。</span><span class="sxs-lookup"><span data-stu-id="1e045-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="1e045-128">如果拒绝值 `123.45` 以采用原始值 `123`，则用户会了解其值不被接受。</span><span class="sxs-lookup"><span data-stu-id="1e045-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="1e045-129">默认情况下，绑定适用于元素的 `onchange` 事件 (`@bind="{PROPERTY OR FIELD}"`)。</span><span class="sxs-lookup"><span data-stu-id="1e045-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="1e045-130">使用 `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` 对其他事件触发绑定。</span><span class="sxs-lookup"><span data-stu-id="1e045-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="1e045-131">对于 `oninput` 事件 (`@bind:event="oninput"`)，在任何引入无法分析的值的击键之后，会进行还原。</span><span class="sxs-lookup"><span data-stu-id="1e045-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="1e045-132">当使用 `oninput` 绑定类型以 `int` 事件为目标时，会阻止用户键入 `.` 字符。</span><span class="sxs-lookup"><span data-stu-id="1e045-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="1e045-133">`.` 字符会立即删除，因此用户会收到仅允许整数的即时反馈。</span><span class="sxs-lookup"><span data-stu-id="1e045-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="1e045-134">在某些情况下，在 `oninput` 事件中还原值并不理想，例如在应该允许用户清除无法解析的 `<input>` 值时。</span><span class="sxs-lookup"><span data-stu-id="1e045-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="1e045-135">替代方案包括：</span><span class="sxs-lookup"><span data-stu-id="1e045-135">Alternatives include:</span></span>

* <span data-ttu-id="1e045-136">不使用 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="1e045-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="1e045-137">使用默认 `onchange` 事件（仅指定 `@bind="{PROPERTY OR FIELD}"`），其中无效值在元素失去焦点之前不会还原。</span><span class="sxs-lookup"><span data-stu-id="1e045-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="1e045-138">绑定到可以为 null 的类型（如 `int?` 或 `string`），并提供自定义逻辑来处理无效条目。</span><span class="sxs-lookup"><span data-stu-id="1e045-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="1e045-139">使用[窗体验证组件](xref:blazor/forms-validation)，如 `InputNumber` 或 `InputDate`。</span><span class="sxs-lookup"><span data-stu-id="1e045-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="1e045-140">窗体验证组件具有用于管理无效输入的内置支持。</span><span class="sxs-lookup"><span data-stu-id="1e045-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="1e045-141">窗体验证组件：</span><span class="sxs-lookup"><span data-stu-id="1e045-141">Form validation components:</span></span>
  * <span data-ttu-id="1e045-142">允许用户提供无效输入并在关联的 `EditContext` 上接收验证错误。</span><span class="sxs-lookup"><span data-stu-id="1e045-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="1e045-143">在 UI 中显示验证错误，而不干扰用户输入其他 webform 数据。</span><span class="sxs-lookup"><span data-stu-id="1e045-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="1e045-144">格式字符串</span><span class="sxs-lookup"><span data-stu-id="1e045-144">Format strings</span></span>

<span data-ttu-id="1e045-145">数据绑定使用 <xref:System.DateTime>[`@bind:format` 处理 ](xref:mvc/views/razor#bind) 格式字符串。</span><span class="sxs-lookup"><span data-stu-id="1e045-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="1e045-146">目前无法使用其他格式表达式，如货币或数字格式。</span><span class="sxs-lookup"><span data-stu-id="1e045-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="1e045-147">在前面的代码中，`<input>` 元素的字段类型 (`type`) 默认为 `text`。</span><span class="sxs-lookup"><span data-stu-id="1e045-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="1e045-148">对于绑定以下 .NET 类型，支持 `@bind:format`：</span><span class="sxs-lookup"><span data-stu-id="1e045-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="1e045-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="1e045-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="1e045-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="1e045-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="1e045-151">`@bind:format` 属性指定要应用于 `value` 元素的 `<input>` 的日期格式。</span><span class="sxs-lookup"><span data-stu-id="1e045-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="1e045-152">该格式还用于在 `onchange` 事件发生时分析值。</span><span class="sxs-lookup"><span data-stu-id="1e045-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="1e045-153">不建议为 `date` 字段类型指定格式，因为 Blazor 具有用于设置日期格式的内置支持。</span><span class="sxs-lookup"><span data-stu-id="1e045-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="1e045-154">尽管提出了建议，但如果使用 `yyyy-MM-dd` 字段类型提供格式，则只有使用 `date` 日期格式才能使绑定正常工作：</span><span class="sxs-lookup"><span data-stu-id="1e045-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="1e045-155">使用组件参数的父级到子级绑定</span><span class="sxs-lookup"><span data-stu-id="1e045-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="1e045-156">绑定可识别组件参数，其中 `@bind-{PROPERTY}` 可以将属性值从父组件向下绑定到子组件。</span><span class="sxs-lookup"><span data-stu-id="1e045-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="1e045-157">在[使用链接绑定的子级到父级绑定](#child-to-parent-binding-with-chained-bind)部分中，介绍了从子级绑定到父级。</span><span class="sxs-lookup"><span data-stu-id="1e045-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="1e045-158">以下子组件 (`ChildComponent`) 具有 `Year` 组件参数和 `YearChanged` 回调：</span><span class="sxs-lookup"><span data-stu-id="1e045-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="1e045-159">`EventCallback<T>` 在 <xref:blazor/event-handling#eventcallback> 中进行了说明。</span><span class="sxs-lookup"><span data-stu-id="1e045-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="1e045-160">以下父组件使用：</span><span class="sxs-lookup"><span data-stu-id="1e045-160">The following parent component uses:</span></span>

* <span data-ttu-id="1e045-161">`ChildComponent` 并将 `ParentYear` 参数从父级绑定到子组件上的 `Year` 参数。</span><span class="sxs-lookup"><span data-stu-id="1e045-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="1e045-162">`onclick` 事件用于触发 `ChangeTheYear` 方法。</span><span class="sxs-lookup"><span data-stu-id="1e045-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="1e045-163">有关更多信息，请参见 <xref:blazor/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="1e045-163">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="1e045-164">加载 `ParentComponent` 会生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="1e045-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="1e045-165">如果通过在 `ParentYear` 中选择按钮来更改 `ParentComponent` 属性的值，则会更新 `Year` 的 `ChildComponent` 属性。</span><span class="sxs-lookup"><span data-stu-id="1e045-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="1e045-166">当重新呈现 `Year` 时，`ParentComponent` 的新值会呈现在 UI 中：</span><span class="sxs-lookup"><span data-stu-id="1e045-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="1e045-167">`Year` 参数是可绑定的，因为它具有与 `YearChanged` 参数类型相匹配的伴随 `Year` 事件。</span><span class="sxs-lookup"><span data-stu-id="1e045-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="1e045-168">按照约定，`<ChildComponent @bind-Year="ParentYear" />` 在本质上等效于编写：</span><span class="sxs-lookup"><span data-stu-id="1e045-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="1e045-169">通常，可以通过包含 `@bind-{PROPRETY}:event` 特性将属性绑定到对应的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="1e045-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="1e045-170">例如，可以使用以下两个特性将属性 `MyProp` 绑定到 `MyEventHandler`：</span><span class="sxs-lookup"><span data-stu-id="1e045-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="1e045-171">使用链接绑定的子级到父级绑定</span><span class="sxs-lookup"><span data-stu-id="1e045-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="1e045-172">一种常见方案是将数据绑定参数链接到组件输出中的页面元素。</span><span class="sxs-lookup"><span data-stu-id="1e045-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="1e045-173">此方案称为链接绑定  ，因为多个级别的绑定会同时进行。</span><span class="sxs-lookup"><span data-stu-id="1e045-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="1e045-174">无法在页面元素中使用 `@bind` 语法实现链接绑定。</span><span class="sxs-lookup"><span data-stu-id="1e045-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="1e045-175">必须单独指定事件处理程序和值。</span><span class="sxs-lookup"><span data-stu-id="1e045-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="1e045-176">但是，父组件可以将 `@bind` 语法用于组件的参数。</span><span class="sxs-lookup"><span data-stu-id="1e045-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="1e045-177">以下 `PasswordField` 组件 (PasswordField.razor  )：</span><span class="sxs-lookup"><span data-stu-id="1e045-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="1e045-178">将 `<input>` 元素的值设置为 `Password` 属性。</span><span class="sxs-lookup"><span data-stu-id="1e045-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="1e045-179">使用 `Password`EventCallback[ 向父组件公开 ](xref:blazor/event-handling#eventcallback) 属性的更改。</span><span class="sxs-lookup"><span data-stu-id="1e045-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="1e045-180">使用 `onclick` 事件触发 `ToggleShowPassword` 方法。</span><span class="sxs-lookup"><span data-stu-id="1e045-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="1e045-181">有关更多信息，请参见 <xref:blazor/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="1e045-181">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

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

<span data-ttu-id="1e045-182">`PasswordField` 组件在另一个组件中使用：</span><span class="sxs-lookup"><span data-stu-id="1e045-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="1e045-183">对前面示例中的密码执行检查或捕获错误：</span><span class="sxs-lookup"><span data-stu-id="1e045-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="1e045-184">为 `Password` 创建支持字段（在下面的示例代码中为 `_password`）。</span><span class="sxs-lookup"><span data-stu-id="1e045-184">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="1e045-185">在 `Password` 资源库中执行检查或捕获错误。</span><span class="sxs-lookup"><span data-stu-id="1e045-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="1e045-186">如果密码的值中使用了空格，则以下示例会向用户提供即时反馈：</span><span class="sxs-lookup"><span data-stu-id="1e045-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

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

## <a name="radio-buttons"></a><span data-ttu-id="1e045-187">单选按钮</span><span class="sxs-lookup"><span data-stu-id="1e045-187">Radio buttons</span></span>

<span data-ttu-id="1e045-188">有关绑定到窗体中的单选按钮的信息，请参阅 <xref:blazor/forms-validation#work-with-radio-buttons>。</span><span class="sxs-lookup"><span data-stu-id="1e045-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
