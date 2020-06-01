---
<span data-ttu-id="69158-101">title:“ASP.NET Core Blazor 数据绑定”author: description：“了解 Blazor 应用中组件和 DOM 元素的数据绑定功能。”</span><span class="sxs-lookup"><span data-stu-id="69158-101">title: 'ASP.NET Core Blazor data binding' author: description: 'Learn about data binding features for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="69158-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="69158-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="69158-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="69158-103">'Blazor'</span></span>
- <span data-ttu-id="69158-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="69158-104">'Identity'</span></span>
- <span data-ttu-id="69158-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="69158-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="69158-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="69158-106">'Razor'</span></span>
- <span data-ttu-id="69158-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="69158-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="69158-108">ASP.NET Core Blazor 数据绑定</span><span class="sxs-lookup"><span data-stu-id="69158-108">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="69158-109">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="69158-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="69158-110"> 组件通过名为 [`@bind`](xref:mvc/views/razor#bind) 的 HTML 元素特性提供了数据绑定功能，该特性具有字段、属性或 Razor 表达式值。</span><span class="sxs-lookup"><span data-stu-id="69158-110"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="69158-111">下面的示例将 `CurrentValue` 属性绑定到文本框的值：</span><span class="sxs-lookup"><span data-stu-id="69158-111">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="69158-112">当文本框失去焦点时，该属性的值会更新。</span><span class="sxs-lookup"><span data-stu-id="69158-112">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="69158-113">仅当呈现组件时（而不是响应属性值更改），才会在 UI 中更新文本框。</span><span class="sxs-lookup"><span data-stu-id="69158-113">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="69158-114">由于组件会在事件处理程序代码执行之后呈现自己，因此在触发事件处理程序之后，通常会在 UI 中反映属性更新。</span><span class="sxs-lookup"><span data-stu-id="69158-114">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="69158-115">将 [`@bind`](xref:mvc/views/razor#bind) 与 `CurrentValue` 属性结合使用 (`<input @bind="CurrentValue" />`) 在本质上等效于以下内容：</span><span class="sxs-lookup"><span data-stu-id="69158-115">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="69158-116">呈现组件时，输入元素的 `value` 来自 `CurrentValue` 属性。</span><span class="sxs-lookup"><span data-stu-id="69158-116">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="69158-117">用户在文本框中键入并更改元素焦点时，会激发 `onchange` 事件并将 `CurrentValue` 属性设置为更改的值。</span><span class="sxs-lookup"><span data-stu-id="69158-117">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="69158-118">实际上，代码生成更加复杂，因为 [`@bind`](xref:mvc/views/razor#bind) 会处理执行类型转换的情况。</span><span class="sxs-lookup"><span data-stu-id="69158-118">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="69158-119">原则上，[`@bind`](xref:mvc/views/razor#bind) 将表达式的当前值与 `value` 属性关联，并使用注册的处理程序处理更改。</span><span class="sxs-lookup"><span data-stu-id="69158-119">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="69158-120">通过同时包含带有 `event` 参数的 `@bind:event` 属性，在其他事件上绑定属性或字段。</span><span class="sxs-lookup"><span data-stu-id="69158-120">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="69158-121">以下示例在 `oninput` 事件上绑定 `CurrentValue` 属性：</span><span class="sxs-lookup"><span data-stu-id="69158-121">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="69158-122">与在元素失去焦点时激发的 `onchange` 不同，`oninput` 在文本框的值更改时激发。</span><span class="sxs-lookup"><span data-stu-id="69158-122">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="69158-123">通过 `@bind-{ATTRIBUTE}:event` 语法使用 `@bind-{ATTRIBUTE}` 可绑定除 `value` 之外的元素属性。</span><span class="sxs-lookup"><span data-stu-id="69158-123">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="69158-124">在下面的示例中，当 `paragraphStyle` 值更改时，段落的样式会更新：</span><span class="sxs-lookup"><span data-stu-id="69158-124">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="69158-125">属性绑定区分大小写：</span><span class="sxs-lookup"><span data-stu-id="69158-125">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="69158-126">`@bind` 有效。</span><span class="sxs-lookup"><span data-stu-id="69158-126">`@bind` is valid.</span></span>
* <span data-ttu-id="69158-127">`@Bind` 和 `@BIND` 无效。</span><span class="sxs-lookup"><span data-stu-id="69158-127">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="69158-128">无法分析的值</span><span class="sxs-lookup"><span data-stu-id="69158-128">Unparsable values</span></span>

<span data-ttu-id="69158-129">如果用户向数据绑定元素提供无法分析的值，则在触发绑定事件时，无法分析的值会自动还原为以前的值。</span><span class="sxs-lookup"><span data-stu-id="69158-129">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="69158-130">请参考以下方案：</span><span class="sxs-lookup"><span data-stu-id="69158-130">Consider the following scenario:</span></span>

* <span data-ttu-id="69158-131">`<input>` 元素绑定到 `int` 类型，其初始值为 `123`：</span><span class="sxs-lookup"><span data-stu-id="69158-131">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="69158-132">用户在页面中将该元素的值更新为 `123.45`，并更改元素焦点。</span><span class="sxs-lookup"><span data-stu-id="69158-132">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="69158-133">在上面的方案中，元素的值会还原为 `123`。</span><span class="sxs-lookup"><span data-stu-id="69158-133">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="69158-134">如果拒绝值 `123.45` 以采用原始值 `123`，则用户会了解其值不被接受。</span><span class="sxs-lookup"><span data-stu-id="69158-134">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="69158-135">默认情况下，绑定适用于元素的 `onchange` 事件 (`@bind="{PROPERTY OR FIELD}"`)。</span><span class="sxs-lookup"><span data-stu-id="69158-135">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="69158-136">使用 `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` 对其他事件触发绑定。</span><span class="sxs-lookup"><span data-stu-id="69158-136">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="69158-137">对于 `oninput` 事件 (`@bind:event="oninput"`)，在任何引入无法分析的值的击键之后，会进行还原。</span><span class="sxs-lookup"><span data-stu-id="69158-137">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="69158-138">当使用 `int` 绑定类型以 `oninput` 事件为目标时，会阻止用户键入 `.` 字符。</span><span class="sxs-lookup"><span data-stu-id="69158-138">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="69158-139">`.` 字符会立即删除，因此用户会收到仅允许整数的即时反馈。</span><span class="sxs-lookup"><span data-stu-id="69158-139">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="69158-140">在某些情况下，在 `oninput` 事件中还原值并不理想，例如在应该允许用户清除无法解析的 `<input>` 值时。</span><span class="sxs-lookup"><span data-stu-id="69158-140">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="69158-141">替代方案包括：</span><span class="sxs-lookup"><span data-stu-id="69158-141">Alternatives include:</span></span>

* <span data-ttu-id="69158-142">不使用 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="69158-142">Don't use the `oninput` event.</span></span> <span data-ttu-id="69158-143">使用默认 `onchange` 事件（仅指定 `@bind="{PROPERTY OR FIELD}"`），其中无效值在元素失去焦点之前不会还原。</span><span class="sxs-lookup"><span data-stu-id="69158-143">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="69158-144">绑定到可以为 null 的类型（如 `int?` 或 `string`），并提供自定义逻辑来处理无效条目。</span><span class="sxs-lookup"><span data-stu-id="69158-144">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="69158-145">使用[窗体验证组件](xref:blazor/forms-validation)，如 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 或 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>。</span><span class="sxs-lookup"><span data-stu-id="69158-145">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="69158-146">窗体验证组件具有用于管理无效输入的内置支持。</span><span class="sxs-lookup"><span data-stu-id="69158-146">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="69158-147">窗体验证组件：</span><span class="sxs-lookup"><span data-stu-id="69158-147">Form validation components:</span></span>
  * <span data-ttu-id="69158-148">允许用户提供无效输入并在关联的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 上接收验证错误。</span><span class="sxs-lookup"><span data-stu-id="69158-148">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="69158-149">在 UI 中显示验证错误，而不干扰用户输入其他 webform 数据。</span><span class="sxs-lookup"><span data-stu-id="69158-149">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="69158-150">格式字符串</span><span class="sxs-lookup"><span data-stu-id="69158-150">Format strings</span></span>

<span data-ttu-id="69158-151">数据绑定使用 `@bind:format` 处理 <xref:System.DateTime> 格式字符串。</span><span class="sxs-lookup"><span data-stu-id="69158-151">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="69158-152">目前无法使用其他格式表达式，如货币或数字格式。</span><span class="sxs-lookup"><span data-stu-id="69158-152">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="69158-153">在前面的代码中，`<input>` 元素的字段类型 (`type`) 默认为 `text`。</span><span class="sxs-lookup"><span data-stu-id="69158-153">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="69158-154">对于绑定以下 .NET 类型，支持 `@bind:format`：</span><span class="sxs-lookup"><span data-stu-id="69158-154">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="69158-155"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="69158-155"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="69158-156"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="69158-156"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="69158-157">`@bind:format` 属性指定要应用于 `<input>` 元素的 `value` 的日期格式。</span><span class="sxs-lookup"><span data-stu-id="69158-157">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="69158-158">该格式还用于在 `onchange` 事件发生时分析值。</span><span class="sxs-lookup"><span data-stu-id="69158-158">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="69158-159">不建议为 `date` 字段类型指定格式，因为 Blazor 具有用于设置日期格式的内置支持。</span><span class="sxs-lookup"><span data-stu-id="69158-159">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="69158-160">尽管提出了建议，但如果使用 `date` 字段类型提供格式，则只有使用 `yyyy-MM-dd` 日期格式才能使绑定正常工作：</span><span class="sxs-lookup"><span data-stu-id="69158-160">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="69158-161">使用组件参数的父级到子级绑定</span><span class="sxs-lookup"><span data-stu-id="69158-161">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="69158-162">绑定可识别组件参数，其中 `@bind-{PROPERTY}` 可以将属性值从父组件向下绑定到子组件。</span><span class="sxs-lookup"><span data-stu-id="69158-162">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="69158-163">在[使用链接绑定的子级到父级绑定](#child-to-parent-binding-with-chained-bind)部分中，介绍了从子级绑定到父级。</span><span class="sxs-lookup"><span data-stu-id="69158-163">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="69158-164">以下子组件 (`ChildComponent`) 具有 `Year` 组件参数和 `YearChanged` 回调：</span><span class="sxs-lookup"><span data-stu-id="69158-164">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="69158-165"><xref:Microsoft.AspNetCore.Components.EventCallback%601> 在 <xref:blazor/event-handling#eventcallback> 中进行了说明。</span><span class="sxs-lookup"><span data-stu-id="69158-165"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="69158-166">以下父组件使用：</span><span class="sxs-lookup"><span data-stu-id="69158-166">The following parent component uses:</span></span>

* <span data-ttu-id="69158-167">`ChildComponent` 并将 `ParentYear` 参数从父级绑定到子组件上的 `Year` 参数。</span><span class="sxs-lookup"><span data-stu-id="69158-167">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="69158-168">`onclick` 事件用于触发 `ChangeTheYear` 方法。</span><span class="sxs-lookup"><span data-stu-id="69158-168">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="69158-169">有关详细信息，请参阅 <xref:blazor/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="69158-169">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="69158-170">加载 `ParentComponent` 会生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="69158-170">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="69158-171">如果通过在 `ParentComponent` 中选择按钮来更改 `ParentYear` 属性的值，则会更新 `ChildComponent` 的 `Year` 属性。</span><span class="sxs-lookup"><span data-stu-id="69158-171">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="69158-172">当重新呈现 `ParentComponent` 时，`Year` 的新值会呈现在 UI 中：</span><span class="sxs-lookup"><span data-stu-id="69158-172">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="69158-173">`Year` 参数是可绑定的，因为它具有与 `Year` 参数类型相匹配的伴随 `YearChanged` 事件。</span><span class="sxs-lookup"><span data-stu-id="69158-173">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="69158-174">按照约定，`<ChildComponent @bind-Year="ParentYear" />` 在本质上等效于编写：</span><span class="sxs-lookup"><span data-stu-id="69158-174">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="69158-175">通常，可以通过包含 `@bind-{PROPRETY}:event` 特性将属性绑定到对应的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="69158-175">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="69158-176">例如，可以使用以下两个特性将属性 `MyProp` 绑定到 `MyEventHandler`：</span><span class="sxs-lookup"><span data-stu-id="69158-176">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="69158-177">使用链接绑定的子级到父级绑定</span><span class="sxs-lookup"><span data-stu-id="69158-177">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="69158-178">一种常见方案是将数据绑定参数链接到组件输出中的页面元素。</span><span class="sxs-lookup"><span data-stu-id="69158-178">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="69158-179">此方案称为链接绑定，因为多个级别的绑定会同时进行。</span><span class="sxs-lookup"><span data-stu-id="69158-179">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="69158-180">无法在页面元素中使用 [`@bind`](xref:mvc/views/razor#bind) 语法实现链接绑定。</span><span class="sxs-lookup"><span data-stu-id="69158-180">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="69158-181">必须单独指定事件处理程序和值。</span><span class="sxs-lookup"><span data-stu-id="69158-181">The event handler and value must be specified separately.</span></span> <span data-ttu-id="69158-182">但是，父组件可以将 [`@bind`](xref:mvc/views/razor#bind) 语法用于组件的参数。</span><span class="sxs-lookup"><span data-stu-id="69158-182">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="69158-183">以下 `PasswordField` 组件 (PasswordField.razor)：</span><span class="sxs-lookup"><span data-stu-id="69158-183">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="69158-184">将 `<input>` 元素的值设置为 `Password` 属性。</span><span class="sxs-lookup"><span data-stu-id="69158-184">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="69158-185">使用 [EventCallback](xref:blazor/event-handling#eventcallback) 向父组件公开 `Password` 属性的更改。</span><span class="sxs-lookup"><span data-stu-id="69158-185">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="69158-186">使用 `onclick` 事件触发 `ToggleShowPassword` 方法。</span><span class="sxs-lookup"><span data-stu-id="69158-186">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="69158-187">有关详细信息，请参阅 <xref:blazor/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="69158-187">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

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

<span data-ttu-id="69158-188">`PasswordField` 组件在另一个组件中使用：</span><span class="sxs-lookup"><span data-stu-id="69158-188">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="69158-189">对前面示例中的密码执行检查或捕获错误：</span><span class="sxs-lookup"><span data-stu-id="69158-189">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="69158-190">为 `Password` 创建支持字段（在下面的示例代码中为 `password`）。</span><span class="sxs-lookup"><span data-stu-id="69158-190">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="69158-191">在 `Password` 资源库中执行检查或捕获错误。</span><span class="sxs-lookup"><span data-stu-id="69158-191">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="69158-192">如果密码的值中使用了空格，则以下示例会向用户提供即时反馈：</span><span class="sxs-lookup"><span data-stu-id="69158-192">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

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

## <a name="radio-buttons"></a><span data-ttu-id="69158-193">单选按钮</span><span class="sxs-lookup"><span data-stu-id="69158-193">Radio buttons</span></span>

<span data-ttu-id="69158-194">有关绑定到窗体中的单选按钮的信息，请参阅 <xref:blazor/forms-validation#work-with-radio-buttons>。</span><span class="sxs-lookup"><span data-stu-id="69158-194">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
