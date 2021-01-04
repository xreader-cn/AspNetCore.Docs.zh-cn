---
title: ASP.NET Core Blazor 数据绑定
author: guardrex
description: 了解 Blazor 应用中组件和 DOM 元素的数据绑定功能。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/data-binding
ms.openlocfilehash: 1f22468c2c9bfb5c3c184e8686f1a8d1b486536d
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486065"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="64665-103">ASP.NET Core Blazor 数据绑定</span><span class="sxs-lookup"><span data-stu-id="64665-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="64665-104">作者：[Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27) 和 [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="64665-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="64665-105">Razor 组件通过名为 [`@bind`](xref:mvc/views/razor#bind) 的 HTML 元素特性提供了数据绑定功能，该特性具有字段、属性或 Razor 表达式值。</span><span class="sxs-lookup"><span data-stu-id="64665-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="64665-106">下面的示例将 `<input>` 元素绑定到 `currentValue` 字段，将`<input>` 元素绑定到 `CurrentValue` 属性：</span><span class="sxs-lookup"><span data-stu-id="64665-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="64665-107">当其中一个元素失去焦点时，将更新其绑定字段或属性。</span><span class="sxs-lookup"><span data-stu-id="64665-107">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="64665-108">仅当呈现组件时（而不是响应字段或属性值更改），才会在 UI 中更新文本框。</span><span class="sxs-lookup"><span data-stu-id="64665-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="64665-109">由于组件会在事件处理程序代码执行之后呈现自己，因此在触发事件处理程序之后，通常会在 UI 中反映字段和属性更新。</span><span class="sxs-lookup"><span data-stu-id="64665-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="64665-110">将 [`@bind`](xref:mvc/views/razor#bind) 与 `CurrentValue` 属性结合使用 (`<input @bind="CurrentValue" />`) 在本质上等效于以下内容：</span><span class="sxs-lookup"><span data-stu-id="64665-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="64665-111">呈现组件时，输入元素的 `value` 来自 `CurrentValue` 属性。</span><span class="sxs-lookup"><span data-stu-id="64665-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="64665-112">用户在文本框中键入并更改元素焦点时，会激发 `onchange` 事件并将 `CurrentValue` 属性设置为更改的值。</span><span class="sxs-lookup"><span data-stu-id="64665-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="64665-113">实际上，代码生成会比这更加复杂，因为 [`@bind`](xref:mvc/views/razor#bind) 会处理执行类型转换的情况。</span><span class="sxs-lookup"><span data-stu-id="64665-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="64665-114">原则上，[`@bind`](xref:mvc/views/razor#bind) 将表达式的当前值与 `value` 属性关联，并使用注册的处理程序处理更改。</span><span class="sxs-lookup"><span data-stu-id="64665-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="64665-115">通过同时包含带有 `event` 参数的 `@bind:event` 属性，在其他事件上绑定属性或字段。</span><span class="sxs-lookup"><span data-stu-id="64665-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="64665-116">以下示例在 `oninput` 事件上绑定 `CurrentValue` 属性：</span><span class="sxs-lookup"><span data-stu-id="64665-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="64665-117">与在元素失去焦点时激发的 `onchange` 不同，`oninput` 在文本框的值更改时激发。</span><span class="sxs-lookup"><span data-stu-id="64665-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="64665-118">属性绑定区分大小写：</span><span class="sxs-lookup"><span data-stu-id="64665-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="64665-119">`@bind` 有效。</span><span class="sxs-lookup"><span data-stu-id="64665-119">`@bind` is valid.</span></span>
* <span data-ttu-id="64665-120">`@Bind` 和 `@BIND` 无效。</span><span class="sxs-lookup"><span data-stu-id="64665-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="64665-121">无法分析的值</span><span class="sxs-lookup"><span data-stu-id="64665-121">Unparsable values</span></span>

<span data-ttu-id="64665-122">如果用户向数据绑定元素提供无法分析的值，则在触发绑定事件时，无法分析的值会自动还原为以前的值。</span><span class="sxs-lookup"><span data-stu-id="64665-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="64665-123">请参考以下方案：</span><span class="sxs-lookup"><span data-stu-id="64665-123">Consider the following scenario:</span></span>

* <span data-ttu-id="64665-124">`<input>` 元素绑定到 `int` 类型，其初始值为 `123`：</span><span class="sxs-lookup"><span data-stu-id="64665-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="64665-125">用户在页面中将该元素的值更新为 `123.45`，并更改元素焦点。</span><span class="sxs-lookup"><span data-stu-id="64665-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="64665-126">在上面的方案中，元素的值会还原为 `123`。</span><span class="sxs-lookup"><span data-stu-id="64665-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="64665-127">如果拒绝值 `123.45` 以采用原始值 `123`，则用户会了解其值不被接受。</span><span class="sxs-lookup"><span data-stu-id="64665-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="64665-128">默认情况下，绑定适用于元素的 `onchange` 事件 (`@bind="{PROPERTY OR FIELD}"`)。</span><span class="sxs-lookup"><span data-stu-id="64665-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="64665-129">使用 `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` 对其他事件触发绑定。</span><span class="sxs-lookup"><span data-stu-id="64665-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="64665-130">对于 `oninput` 事件 (`@bind:event="oninput"`)，在任何引入无法分析的值的击键之后，会进行还原。</span><span class="sxs-lookup"><span data-stu-id="64665-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="64665-131">当使用 `int` 绑定类型以 `oninput` 事件为目标时，会阻止用户键入 `.` 字符。</span><span class="sxs-lookup"><span data-stu-id="64665-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="64665-132">`.` 字符会立即删除，因此用户会收到仅允许整数的即时反馈。</span><span class="sxs-lookup"><span data-stu-id="64665-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="64665-133">在某些情况下，在 `oninput` 事件中还原值并不理想，例如在应该允许用户清除无法解析的 `<input>` 值时。</span><span class="sxs-lookup"><span data-stu-id="64665-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="64665-134">替代方案包括：</span><span class="sxs-lookup"><span data-stu-id="64665-134">Alternatives include:</span></span>

* <span data-ttu-id="64665-135">不使用 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="64665-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="64665-136">使用默认 `onchange` 事件（仅指定 `@bind="{PROPERTY OR FIELD}"`），其中无效值在元素失去焦点之前不会还原。</span><span class="sxs-lookup"><span data-stu-id="64665-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="64665-137">绑定到可以为 null 的类型（如 `int?` 或 `string`），并提供自定义逻辑来处理无效条目。</span><span class="sxs-lookup"><span data-stu-id="64665-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="64665-138">使用[窗体验证组件](xref:blazor/forms-validation)，如 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 或 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>。</span><span class="sxs-lookup"><span data-stu-id="64665-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="64665-139">窗体验证组件具有用于管理无效输入的内置支持。</span><span class="sxs-lookup"><span data-stu-id="64665-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="64665-140">有关详细信息，请参阅 <xref:blazor/forms-validation>。</span><span class="sxs-lookup"><span data-stu-id="64665-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="64665-141">窗体验证组件：</span><span class="sxs-lookup"><span data-stu-id="64665-141">Form validation components:</span></span>
  * <span data-ttu-id="64665-142">允许用户提供无效输入并在关联的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 上接收验证错误。</span><span class="sxs-lookup"><span data-stu-id="64665-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="64665-143">在 UI 中显示验证错误，而不干扰用户输入其他 webform 数据。</span><span class="sxs-lookup"><span data-stu-id="64665-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="64665-144">格式字符串</span><span class="sxs-lookup"><span data-stu-id="64665-144">Format strings</span></span>

<span data-ttu-id="64665-145">数据绑定使用 `@bind:format` 处理 <xref:System.DateTime> 格式字符串。</span><span class="sxs-lookup"><span data-stu-id="64665-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="64665-146">目前无法使用其他格式表达式，如货币或数字格式。</span><span class="sxs-lookup"><span data-stu-id="64665-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="64665-147">在前面的代码中，`<input>` 元素的字段类型 (`type`) 默认为 `text`。</span><span class="sxs-lookup"><span data-stu-id="64665-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="64665-148">对于绑定以下 .NET 类型，支持 `@bind:format`：</span><span class="sxs-lookup"><span data-stu-id="64665-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="64665-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="64665-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="64665-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="64665-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="64665-151">`@bind:format` 属性指定要应用于 `<input>` 元素的 `value` 的日期格式。</span><span class="sxs-lookup"><span data-stu-id="64665-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="64665-152">该格式还用于在 `onchange` 事件发生时分析值。</span><span class="sxs-lookup"><span data-stu-id="64665-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="64665-153">不建议为 `date` 字段类型指定格式，因为 Blazor 具有用于设置日期格式的内置支持。</span><span class="sxs-lookup"><span data-stu-id="64665-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="64665-154">尽管提出了建议，但如果使用 `date` 字段类型提供格式，则只有使用 `yyyy-MM-dd` 日期格式才能使绑定正常工作：</span><span class="sxs-lookup"><span data-stu-id="64665-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a><span data-ttu-id="64665-155">与组件参数绑定</span><span class="sxs-lookup"><span data-stu-id="64665-155">Binding with component parameters</span></span>

<span data-ttu-id="64665-156">常见场景是将子组件中的属性绑定到其父组件中的属性。</span><span class="sxs-lookup"><span data-stu-id="64665-156">A common scenario is binding a property in a child component to a property in its parent.</span></span> <span data-ttu-id="64665-157">此方案称为链接绑定，因为多个级别的绑定会同时进行。</span><span class="sxs-lookup"><span data-stu-id="64665-157">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="64665-158">通过[组件参数](xref:blazor/components/index#component-parameters)，可使用 `@bind-{PROPERTY OR FIELD}` 语法绑定父组件的属性和字段。</span><span class="sxs-lookup"><span data-stu-id="64665-158">[Component parameters](xref:blazor/components/index#component-parameters) permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="64665-159">不能在子组件中使用 [`@bind`](xref:mvc/views/razor#bind) 语法来实现链接绑定。</span><span class="sxs-lookup"><span data-stu-id="64665-159">Chained binds can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="64665-160">必须单独指定事件处理程序和值，以支持从子组件更新父组件中的属性。</span><span class="sxs-lookup"><span data-stu-id="64665-160">An event handler and value must be specified separately to support updating the property in the parent from the child component.</span></span>

<span data-ttu-id="64665-161">父组件仍利用 [`@bind`](xref:mvc/views/razor#bind) 语法来设置与子组件的数据绑定。</span><span class="sxs-lookup"><span data-stu-id="64665-161">The parent component still leverages the [`@bind`](xref:mvc/views/razor#bind) syntax to set up the data-binding with the child component.</span></span>

<span data-ttu-id="64665-162">以下 `Child` 组件 (`Shared/Child.razor`) 具有 `Year` 组件参数和 `YearChanged` 回调：</span><span class="sxs-lookup"><span data-stu-id="64665-162">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

<span data-ttu-id="64665-163">回调 (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) 必须命名为组件参数名后跟“`Changed`”后缀 (`{PARAMETER NAME}Changed`)。</span><span class="sxs-lookup"><span data-stu-id="64665-163">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="64665-164">在上一示例中，回调名为 `YearChanged`。</span><span class="sxs-lookup"><span data-stu-id="64665-164">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="64665-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> 调用与提供的参数进行绑定相关联的委托，并为已更改的属性调度事件通知。</span><span class="sxs-lookup"><span data-stu-id="64665-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invokes the delegate associated with the binding with the provided argument and dispatches an event notification for the changed property.</span></span>

<span data-ttu-id="64665-166">在下面的 `Parent` 组件 (`Parent.razor`) 中，`year` 字段绑定到子组件的 `Year` 参数：</span><span class="sxs-lookup"><span data-stu-id="64665-166">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="64665-167">`Year` 参数是可绑定的，因为它具有与 `Year` 参数类型相匹配的伴随 `YearChanged` 事件。</span><span class="sxs-lookup"><span data-stu-id="64665-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="64665-168">按照约定，可通过包含分配到处理程序的 `@bind-{PROPERTY}:event` 特性，将属性绑定到对应的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="64665-168">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="64665-169">`<Child @bind-Year="year" />` 等效于此写入：</span><span class="sxs-lookup"><span data-stu-id="64665-169">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="64665-170">在更复杂和实际的示例中，以下 `PasswordField` 组件 (`PasswordField.razor`)：</span><span class="sxs-lookup"><span data-stu-id="64665-170">In a more sophisticated and real-world example, the following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="64665-171">将 `<input>` 元素的值设置为 `password` 字段。</span><span class="sxs-lookup"><span data-stu-id="64665-171">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="64665-172">将 `Password` 属性的更改公开给父组件，其中 [`EventCallback`](xref:blazor/components/event-handling#eventcallback) 以子级 `password` 字段的当前值作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="64665-172">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="64665-173">使用 `onclick` 事件触发 `ToggleShowPassword` 方法。</span><span class="sxs-lookup"><span data-stu-id="64665-173">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="64665-174">有关详细信息，请参阅 <xref:blazor/components/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="64665-174">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="64665-175">`PasswordField` 组件在另一个组件中使用：</span><span class="sxs-lookup"><span data-stu-id="64665-175">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="64665-176">在调用绑定的委托的方法中执行检查或捕获错误。</span><span class="sxs-lookup"><span data-stu-id="64665-176">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="64665-177">如果密码的值中使用了空格，则以下示例会向用户提供即时反馈：</span><span class="sxs-lookup"><span data-stu-id="64665-177">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="64665-178">有关 <xref:Microsoft.AspNetCore.Components.EventCallback%601> 的详细信息，请参阅 <xref:blazor/components/event-handling#eventcallback>。</span><span class="sxs-lookup"><span data-stu-id="64665-178">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="64665-179">绑定到两个以上的组件</span><span class="sxs-lookup"><span data-stu-id="64665-179">Bind across more than two components</span></span>

<span data-ttu-id="64665-180">可以绑定到任意数量的嵌套组件，但必须采用单向数据流：</span><span class="sxs-lookup"><span data-stu-id="64665-180">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="64665-181">更改通知沿层次结构向上传递。</span><span class="sxs-lookup"><span data-stu-id="64665-181">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="64665-182">新参数值按层次结构向下传递。</span><span class="sxs-lookup"><span data-stu-id="64665-182">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="64665-183">推荐的常见方法是仅将基础数据存储在父组件中，以避免对必须更新的状态产生任何混淆。</span><span class="sxs-lookup"><span data-stu-id="64665-183">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="64665-184">下面的组件演示了上述概念：</span><span class="sxs-lookup"><span data-stu-id="64665-184">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="64665-185">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="64665-185">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="64665-186">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="64665-186">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-GrandchildMessage="BoundValue" />
</div>

@code {
    [Parameter]
    public string ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private string BoundValue
    {
        get => ChildMessage;
        set => ChildMessageChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="64665-187">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="64665-187">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="64665-188">有关适用于跨不必嵌套的组件在内存中共享数据的可选方法，请参阅 <xref:blazor/state-management> 的内存中状态容器服务。</span><span class="sxs-lookup"><span data-stu-id="64665-188">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see the *In-memory state container service* section of  <xref:blazor/state-management>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="64665-189">其他资源</span><span class="sxs-lookup"><span data-stu-id="64665-189">Additional resources</span></span>

* [<span data-ttu-id="64665-190">绑定到窗体中的单选按钮</span><span class="sxs-lookup"><span data-stu-id="64665-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="64665-191">将 `<select>` 元素选项绑定到窗体中的 C# 对象 `null` 值</span><span class="sxs-lookup"><span data-stu-id="64665-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
