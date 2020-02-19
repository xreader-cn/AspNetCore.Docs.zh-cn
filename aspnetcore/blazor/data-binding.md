---
title: ASP.NET Core Blazor 数据绑定
author: guardrex
description: 了解 Blazor 应用中组件和 DOM 元素的数据绑定方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: c38e6095d4e93d3eead10fa8bb0356b2113bb220
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453156"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET Core Blazor 数据绑定

作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)

对组件和 DOM 元素的数据绑定都是通过[`@bind`](xref:mvc/views/razor#bind)特性来完成的。 下面的示例将 `CurrentValue` 属性绑定到文本框的值：

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

当文本框失去焦点时，会更新该属性的值。

仅当呈现组件时，才会在 UI 中更新文本框，而不是响应更改属性值。 由于在事件处理程序代码执行后组件会自行呈现，因此在事件处理程序触发后，属性更新*通常*会立即反映在 UI 中。

结合使用 `@bind` 与 `CurrentValue` 属性（`<input @bind="CurrentValue" />`）本质上等效于以下内容：

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

呈现组件时，输入元素的 `value` 来自 `CurrentValue` 属性。 当用户在文本框中键入内容并更改元素焦点时，将激发 `onchange` 事件并将 `CurrentValue` 属性设置为更改的值。 实际上，代码生成更复杂，因为 `@bind` 处理执行类型转换的情况。 原则上，`@bind` 将表达式的当前值与 `value` 属性相关联，并使用注册的处理程序来处理更改。

除了使用 `@bind` 语法处理 `onchange` 事件之外，还可以使用其他事件来绑定属性或字段，方法是使用 `event` 参数（[`@bind-value:event`](xref:mvc/views/razor#bind)）指定[`@bind-value`](xref:mvc/views/razor#bind)属性。 下面的示例将绑定 `oninput` 事件的 `CurrentValue` 属性：

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

与 `onchange`不同，后者会在元素失去焦点时触发，`oninput` 在文本框的值更改时激发。

前面的示例中的 `@bind-value` 将绑定：

* 元素的 `value` 属性的指定表达式（`CurrentValue`）。
* `@bind-value:event`指定的事件的 change 事件委托。

### <a name="unparsable-values"></a>不可分析值

当用户向数据绑定元素提供无法分析的值时，触发绑定事件时，无法分析的值将自动恢复为其以前的值。

请考虑以下情形：

* `<input>` 元素绑定到 `int` 类型，其初始值为 `123`：

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* 用户更新元素的值以便在页面中 `123.45`，并更改元素焦点。

在上述方案中，将元素的值还原为 `123`。 如果拒绝值 `123.45` 以保证 `123`的原始值，则用户将知道其值不被接受。

默认情况下，绑定适用于元素的 `onchange` 事件（`@bind="{PROPERTY OR FIELD}"`）。 使用 `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` 设置不同的事件。 对于 `oninput` 事件（`@bind-value:event="oninput"`），重新确定会在引入了可分析值的任何击键之后发生。 当以 `int`绑定类型为 `oninput` 事件定位时，将阻止用户键入 `.` 字符。 会立即删除 `.` 字符，因此用户会立即收到仅允许整数的反馈。 在某些情况下，在 `oninput` 事件上恢复该值的情况并不理想，例如当用户应允许清除无法解析的 `<input>` 值时。 替代项包括：

* 不要使用 `oninput` 事件。 使用默认 `onchange` 事件（`@bind="{PROPERTY OR FIELD}"`），其中无效值直到元素失去焦点时才会被还原。
* 绑定到可为 null 的类型（如 `int?` 或 `string`），并提供自定义逻辑来处理无效项。
* 使用[窗体验证组件](xref:blazor/forms-validation)，如 `InputNumber` 或 `InputDate`。 窗体验证组件具有用于管理无效输入的内置支持。 窗体验证组件：
  * 允许用户提供无效输入并在关联的 `EditContext`上收到验证错误。
  * 在 UI 中显示验证错误，不干扰用户输入其他 webform 数据。

### <a name="format-strings"></a>格式字符串

数据绑定使用[`@bind:format`](xref:mvc/views/razor#bind)<xref:System.DateTime> 格式字符串。 现在不能使用其他格式的表达式，如货币或数字格式。

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

在前面的代码中，`<input>` 元素的字段类型（`type`）默认为 `text`。 支持 `@bind:format` 绑定以下 .NET 类型：

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` 特性指定要应用于 `<input>` 元素的 `value` 的日期格式。 此格式还用于分析 `onchange` 事件发生时的值。

不建议为 `date` 字段类型指定格式，因为 Blazor 具有对日期进行格式设置的内置支持。 尽管建议，但如果使用 `date` 字段类型提供格式，则仅使用 `yyyy-MM-dd` 日期格式才能正常工作：

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

### <a name="parent-to-child-binding-with-component-parameters"></a>带有组件参数的父到子绑定

绑定可识别组件参数，其中 `@bind-{property}` 可以将属性值从父组件向下绑定到子组件。 [使用链接绑定部分从子对父绑定](#child-to-parent-binding-with-chained-bind)中介绍了从子级到父级的绑定。

以下子组件（`ChildComponent`）具有 `Year` 组件参数和 `YearChanged` 回调：

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

<xref:blazor/event-handling#eventcallback>中对 `EventCallback<T>` 进行了说明。

以下父组件使用：

* `ChildComponent`，并将 `ParentYear` 参数从父级绑定到子组件上的 `Year` 参数。
* `onclick` 事件用于触发 `ChangeTheYear` 方法。 有关详细信息，请参阅 <xref:blazor/event-handling>。

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

加载 `ParentComponent` 会生成以下标记：

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

如果通过在 `ParentComponent`中选择按钮更改了 `ParentYear` 属性的值，则将更新 `ChildComponent` 的 `Year` 属性。 当 `ParentComponent` 重新呈现时，`Year` 的新值将呈现在 UI 中：

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` 参数是可绑定的，因为它具有与 `Year` 参数类型相匹配的伴随 `YearChanged` 事件。

按照约定，`<ChildComponent @bind-Year="ParentYear" />` 本质上等同于编写：

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

通常，可以使用 `@bind-property:event` 特性将属性绑定到相应的事件处理程序。 例如，可以使用以下两个属性将属性 `MyProp` 绑定到 `MyEventHandler`：

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

### <a name="child-to-parent-binding-with-chained-bind"></a>具有链接绑定的子对父绑定

常见的情况是将数据绑定参数链接到组件输出中的页元素。 此方案称为*链接绑定*，因为多个级别的绑定同时发生。

无法使用页面元素中 `@bind` 语法来实现链接绑定。 必须单独指定事件处理程序和值。 但是，父组件可以将 `@bind` 语法与组件的参数一起使用。

以下 `PasswordField` 组件（*self.passwordfield.text*）：

* 将 `<input>` 元素的值设置为 `Password` 属性。
* 使用[EventCallback](xref:blazor/event-handling#eventcallback)向父组件公开 `Password` 属性的更改。
* 使用 `onclick` 事件来触发 `ToggleShowPassword` 方法。 有关详细信息，请参阅 <xref:blazor/event-handling>。

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

`PasswordField` 组件用于另一个组件：

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

对上述示例中的密码执行检查或陷阱错误：

* 为 `Password` 创建支持字段（在下面的示例代码中`_password`）。
* 在 `Password` 资源库中执行检查或陷阱错误。

如果密码的值中使用了空格，则以下示例向用户提供即时反馈：

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

### <a name="radio-buttons"></a>单选按钮

有关绑定到窗体中的单选按钮的详细信息，请参阅 <xref:blazor/forms-validation#work-with-radio-buttons>。
