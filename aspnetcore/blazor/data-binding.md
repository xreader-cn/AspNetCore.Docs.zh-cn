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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: 3b419b4738bd3f434cf4a9d8ccdf3b3af86ba1d5
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "83998492"
---
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor 数据绑定

作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

Razor 组件通过名为 [`@bind`](xref:mvc/views/razor#bind) 的 HTML 元素特性提供了数据绑定功能，该特性具有字段、属性或 Razor 表达式值。

下面的示例将 `CurrentValue` 属性绑定到文本框的值：

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

当文本框失去焦点时，该属性的值会更新。

仅当呈现组件时（而不是响应属性值更改），才会在 UI 中更新文本框。 由于组件会在事件处理程序代码执行之后呈现自己，因此在触发事件处理程序之后，通常会在 UI 中反映属性更新。

将 [`@bind`](xref:mvc/views/razor#bind) 与 `CurrentValue` 属性结合使用 (`<input @bind="CurrentValue" />`) 在本质上等效于以下内容：

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

呈现组件时，输入元素的 `value` 来自 `CurrentValue` 属性。 用户在文本框中键入并更改元素焦点时，会激发 `onchange` 事件并将 `CurrentValue` 属性设置为更改的值。 实际上，代码生成更加复杂，因为 [`@bind`](xref:mvc/views/razor#bind) 会处理执行类型转换的情况。 原则上，[`@bind`](xref:mvc/views/razor#bind) 将表达式的当前值与 `value` 属性关联，并使用注册的处理程序处理更改。

通过同时包含带有 `event` 参数的 `@bind:event` 属性，在其他事件上绑定属性或字段。 以下示例在 `oninput` 事件上绑定 `CurrentValue` 属性：

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

与在元素失去焦点时激发的 `onchange` 不同，`oninput` 在文本框的值更改时激发。

通过 `@bind-{ATTRIBUTE}:event` 语法使用 `@bind-{ATTRIBUTE}` 可绑定除 `value` 之外的元素属性。 在下面的示例中，当 `paragraphStyle` 值更改时，段落的样式会更新：

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

属性绑定区分大小写：

* `@bind` 有效。
* `@Bind` 和 `@BIND` 无效。

## <a name="unparsable-values"></a>无法分析的值

如果用户向数据绑定元素提供无法分析的值，则在触发绑定事件时，无法分析的值会自动还原为以前的值。

请参考以下方案：

* `<input>` 元素绑定到 `int` 类型，其初始值为 `123`：

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* 用户在页面中将该元素的值更新为 `123.45`，并更改元素焦点。

在上面的方案中，元素的值会还原为 `123`。 如果拒绝值 `123.45` 以采用原始值 `123`，则用户会了解其值不被接受。

默认情况下，绑定适用于元素的 `onchange` 事件 (`@bind="{PROPERTY OR FIELD}"`)。 使用 `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` 对其他事件触发绑定。 对于 `oninput` 事件 (`@bind:event="oninput"`)，在任何引入无法分析的值的击键之后，会进行还原。 当使用 `int` 绑定类型以 `oninput` 事件为目标时，会阻止用户键入 `.` 字符。 `.` 字符会立即删除，因此用户会收到仅允许整数的即时反馈。 在某些情况下，在 `oninput` 事件中还原值并不理想，例如在应该允许用户清除无法解析的 `<input>` 值时。 替代方案包括：

* 不使用 `oninput` 事件。 使用默认 `onchange` 事件（仅指定 `@bind="{PROPERTY OR FIELD}"`），其中无效值在元素失去焦点之前不会还原。
* 绑定到可以为 null 的类型（如 `int?` 或 `string`），并提供自定义逻辑来处理无效条目。
* 使用[窗体验证组件](xref:blazor/forms-validation)，如 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 或 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>。 窗体验证组件具有用于管理无效输入的内置支持。 窗体验证组件：
  * 允许用户提供无效输入并在关联的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 上接收验证错误。
  * 在 UI 中显示验证错误，而不干扰用户输入其他 webform 数据。

## <a name="format-strings"></a>格式字符串

数据绑定使用 `@bind:format` 处理 <xref:System.DateTime> 格式字符串。 目前无法使用其他格式表达式，如货币或数字格式。

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

在前面的代码中，`<input>` 元素的字段类型 (`type`) 默认为 `text`。 对于绑定以下 .NET 类型，支持 `@bind:format`：

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` 属性指定要应用于 `<input>` 元素的 `value` 的日期格式。 该格式还用于在 `onchange` 事件发生时分析值。

不建议为 `date` 字段类型指定格式，因为 Blazor 具有用于设置日期格式的内置支持。 尽管提出了建议，但如果使用 `date` 字段类型提供格式，则只有使用 `yyyy-MM-dd` 日期格式才能使绑定正常工作：

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>使用组件参数的父级到子级绑定

绑定可识别组件参数，其中 `@bind-{PROPERTY}` 可以将属性值从父组件向下绑定到子组件。 在[使用链接绑定的子级到父级绑定](#child-to-parent-binding-with-chained-bind)部分中，介绍了从子级绑定到父级。

以下子组件 (`ChildComponent`) 具有 `Year` 组件参数和 `YearChanged` 回调：

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

<xref:Microsoft.AspNetCore.Components.EventCallback%601> 在 <xref:blazor/event-handling#eventcallback> 中进行了说明。

以下父组件使用：

* `ChildComponent` 并将 `ParentYear` 参数从父级绑定到子组件上的 `Year` 参数。
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

如果通过在 `ParentComponent` 中选择按钮来更改 `ParentYear` 属性的值，则会更新 `ChildComponent` 的 `Year` 属性。 当重新呈现 `ParentComponent` 时，`Year` 的新值会呈现在 UI 中：

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` 参数是可绑定的，因为它具有与 `Year` 参数类型相匹配的伴随 `YearChanged` 事件。

按照约定，`<ChildComponent @bind-Year="ParentYear" />` 在本质上等效于编写：

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

通常，可以通过包含 `@bind-{PROPRETY}:event` 特性将属性绑定到对应的事件处理程序。 例如，可以使用以下两个特性将属性 `MyProp` 绑定到 `MyEventHandler`：

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>使用链接绑定的子级到父级绑定

一种常见方案是将数据绑定参数链接到组件输出中的页面元素。 此方案称为链接绑定，因为多个级别的绑定会同时进行。

无法在页面元素中使用 [`@bind`](xref:mvc/views/razor#bind) 语法实现链接绑定。 必须单独指定事件处理程序和值。 但是，父组件可以将 [`@bind`](xref:mvc/views/razor#bind) 语法用于组件的参数。

以下 `PasswordField` 组件 (PasswordField.razor)：

* 将 `<input>` 元素的值设置为 `Password` 属性。
* 使用 [EventCallback](xref:blazor/event-handling#eventcallback) 向父组件公开 `Password` 属性的更改。
* 使用 `onclick` 事件触发 `ToggleShowPassword` 方法。 有关详细信息，请参阅 <xref:blazor/event-handling>。

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

`PasswordField` 组件在另一个组件中使用：

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

对前面示例中的密码执行检查或捕获错误：

* 为 `Password` 创建支持字段（在下面的示例代码中为 `password`）。
* 在 `Password` 资源库中执行检查或捕获错误。

如果密码的值中使用了空格，则以下示例会向用户提供即时反馈：

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

## <a name="radio-buttons"></a>单选按钮

有关绑定到窗体中的单选按钮的信息，请参阅 <xref:blazor/forms-validation#work-with-radio-buttons>。
