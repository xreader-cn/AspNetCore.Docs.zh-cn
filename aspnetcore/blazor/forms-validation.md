---
title: ASP.NET Core Blazor 窗体和验证
author: guardrex
description: 了解如何在 Blazor中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 2758bcbbc76c8a59716fe224dd2deb4ca8c06929
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726893"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a>ASP.NET Core [!OP.NO-LOC(Blazor)] 窗体和验证

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

使用[数据批注](xref:mvc/models/validation)[!OP.NO-LOC(Blazor)] 支持窗体和验证。

以下 `ExampleModel` 类型使用数据批注定义验证逻辑：

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

使用 `EditForm` 组件定义窗体。 下面的窗体演示典型的元素、组件和 Razor 代码：

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

在上面的示例中：

* 窗体使用 `ExampleModel` 类型中定义的验证来验证 `name` 字段中的用户输入。 该模型是在组件的 `@code` 块中创建的，并保存在私有字段（`_exampleModel`）中。 该字段将分配给 `<EditForm>` 元素的 `Model` 特性。
* `InputText` 组件 `@bind-Value` 绑定：
  * 模型属性（`_exampleModel.Name`）到 `InputText` 组件的 `Value` 属性。
  * `InputText` 组件的 `ValueChanged` 属性的 change 事件委托。
* `DataAnnotationsValidator` 组件使用数据批注附加验证支持。
* `ValidationSummary` 组件汇总验证消息。
* 当窗体成功提交（通过验证）时，将触发 `HandleValidSubmit`。

可使用一组内置输入组件来接收和验证用户输入。 当输入发生更改和提交窗体时，将对其进行验证。 下表显示了可用的输入组件。

| 输入组件 | 呈现为&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

所有输入组件（包括 `EditForm`）都支持任意属性。 与组件参数不匹配的任何属性将添加到呈现的 HTML 元素中。

输入组件提供默认行为，用于验证编辑和更改其 CSS 类以反映字段状态。 某些组件包含有用的分析逻辑。 例如，`InputDate` 和 `InputNumber` 通过将其注册为验证错误来适当地处理无法分析的值。 可以接受 null 值的类型还支持目标字段的为空性（例如 `int?`）。

以下 `Starship` 类型使用比之前 `ExampleModel`更多的属性和数据批注集定义验证逻辑：

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

在前面的示例中，`Description` 是可选的，因为不存在任何数据批注。

以下窗体使用 `Starship` 模型中定义的验证来验证用户输入：

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

`EditForm` 创建一个 `EditContext` 作为[级联值](xref:blazor/components#cascading-values-and-parameters)，用于跟踪有关编辑过程的元数据，包括已修改的字段和当前验证消息。 `EditForm` 还为有效提交和无效提交（`OnValidSubmit`、`OnInvalidSubmit`）提供便利事件。 或者，使用 `OnSubmit` 使用自定义验证代码来触发验证和检查字段值。

在下例中：

* 选择 "**提交**" 按钮时，将运行 `HandleSubmit` 方法。
* 使用窗体的 `EditContext`验证窗体。
* 通过将 `EditContext` 传递给调用服务器上的 web API 终结点的 `ServerValidate` 方法来进一步验证窗体（*未显示*）。
* 根据客户端和服务器端验证的结果，运行其他代码，方法是检查 `isValid`。

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>基于输入事件的 InputText

使用 `InputText` 组件创建使用 `input` 事件而不是 `change` 事件的自定义组件。

使用以下标记创建组件，并使用组件，就像使用 `InputText` 一样：

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>使用单选按钮

使用窗体中的单选按钮时，数据绑定的处理方式与其他元素不同，因为单选按钮作为一个组进行计算。 每个单选按钮的值都是固定的，但单选按钮组的值是所选单选按钮的值。 以下示例介绍如何：

* 处理单选按钮组的数据绑定。
* 使用自定义 `InputRadio` 组件支持验证。

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

以下 `EditForm` 使用上述 `InputRadio` 组件获取和验证用户的评级：

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a>验证支持

`DataAnnotationsValidator` 组件使用数据批注将验证支持附加到级联 `EditContext`。 使用数据批注启用验证支持需要此显式手势。 若要使用不同于数据批注的验证系统，请将 `DataAnnotationsValidator` 替换为自定义实现。 ASP.NET Core 实现可在引用源： [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)中进行检查。

Blazor 执行两种类型的验证：

* 当用户在字段外切换时，将执行*字段验证*。 在字段验证过程中，`DataAnnotationsValidator` 组件将所有报告的验证结果与该字段相关联。
* 当用户提交窗体时，将执行*模型验证*。 在模型验证过程中，`DataAnnotationsValidator` 组件尝试基于验证结果报告的成员名称确定字段。 与单个成员无关的验证结果与模型关联，而不是与字段关联。

### <a name="validation-summary-and-validation-message-components"></a>验证摘要和验证消息组件

`ValidationSummary` 组件汇总了与[验证摘要标记帮助](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)程序类似的所有验证消息：

```razor
<ValidationSummary />
```

使用 `Model` 参数输出特定模型的验证消息：
  
```razor
<ValidationSummary Model="@_starship" />
```

`ValidationMessage` 组件显示特定字段的验证消息，这类似于[验证消息标记帮助器](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)。 使用 `For` 特性指定验证字段，并使用命名模型属性的 lambda 表达式指定：

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

`ValidationMessage` 和 `ValidationSummary` 组件支持任意属性。 与组件参数不匹配的任何属性将添加到生成的 `<div>` 或 `<ul>` 元素。

### <a name="custom-validation-attributes"></a>自定义验证特性

若要确保在使用[自定义验证属性](xref:mvc/models/validation#custom-attributes)时验证结果与字段正确关联，请在创建 <xref:System.ComponentModel.DataAnnotations.ValidationResult>时传递验证上下文的 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>：

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor 数据批注验证包

[AspNetCore.Blazor。DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)是使用 `DataAnnotationsValidator` 组件填充验证体验缺口的包。 包当前正在*试验*。

### <a name="compareproperty-attribute"></a>[CompareProperty] 属性

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 `DataAnnotationsValidator` 组件，因为它不会将验证结果与特定成员关联。 这可能会导致字段级验证之间的行为不一致，并在提交时验证整个模型。 [AspNetCore.Blazor。DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *实验*包引入了一个附加的验证特性 `ComparePropertyAttribute`，该特性围绕这些限制。 在 Blazor 应用中，`[CompareProperty]` 是 `[Compare]` 属性的直接替换。

### <a name="nested-models-collection-types-and-complex-types"></a>嵌套模型、集合类型和复杂类型

Blazor 提供对通过内置 `DataAnnotationsValidator`使用数据批注验证窗体输入的支持。 但是，`DataAnnotationsValidator` 仅验证绑定到不是集合或复杂类型属性的窗体的模型的顶级属性。

若要验证绑定模型的整个对象关系图，包括集合和复杂类型属性，请使用BlazorAspNetCore 提供的 `ObjectGraphDataAnnotationsValidator` 。 [DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)包：

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

用 `[ValidateComplexType]`批注模型属性。 在下面的模型类中，`ShipDescription` 类包含在将模型绑定到窗体时要验证的其他数据批注：

*Starship.cs*：

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

*ShipDescription.cs*：

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>基于窗体验证启用 "提交" 按钮

若要启用和禁用基于窗体验证的 "提交" 按钮：

* 使用窗体的 `EditContext` 在初始化组件时分配模型。
* 验证上下文的 `OnFieldChanged` 回调中的窗体，以启用和禁用 "提交" 按钮。

```razor
<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);

        _editContext.OnFieldChanged += (_, __) =>
        {
            _formInvalid = !_editContext.Validate();
            StateHasChanged();
        };
    }
}
```

在前面的示例中，将 `_formInvalid` 设置为 `false` 如果：

* 该窗体预加载了有效的默认值。
* 您希望在加载窗体时启用 "提交" 按钮。

上述方法的副作用是，在用户与任何一个字段交互后，使用无效的字段填充 `ValidationSummary` 组件。 可以通过以下方式之一解决此方案：

* 请勿使用窗体上的 `ValidationSummary` 组件。
* 选择 "提交" 按钮（例如，在 `HandleValidSubmit` 方法中）时，使 `ValidationSummary` 组件可见。

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
