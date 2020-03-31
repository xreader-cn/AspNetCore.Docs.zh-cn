---
title: ASP.NET Core Blazor 窗体和验证
author: guardrex
description: 了解如何在 Blazor 中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218955"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor 窗体和验证

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

Blazor 使用[数据注释](xref:mvc/models/validation)支持窗体和验证。

下面的 `ExampleModel` 类型使用数据注释定义验证逻辑：

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

窗体是使用 `EditForm` 组件定义的。 以下窗体演示了典型的元素、组件和 Razor 代码：

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

* 该窗体使用 `ExampleModel` 类型中定义的验证来验证 `name` 字段中的用户输入。 该模型在组件的 `@code` 块中创建，并保存在私有字段 (`_exampleModel`) 中。 该字段分配给 `<EditForm>` 元素的 `Model` 属性。
* `InputText` 组件的 `@bind-Value` 进行以下绑定：
  * 将模型属性 (`_exampleModel.Name`) 绑定到 `InputText` 组件的 `Value` 属性。
  * 将更改事件委托绑定到 `InputText` 组件的 `ValueChanged` 属性。
* `DataAnnotationsValidator` 组件使用数据注释附加验证支持。
* `ValidationSummary` 组件汇总验证消息。
* 窗体成功提交（通过验证）时触发 `HandleValidSubmit`。

可使用一组内置的输入组件来接收和验证用户输入。 当更改输入和提交窗体时，将验证输入。 下表显示了可用的输入组件。

| 输入组件 | 呈现为&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

所有输入组件（包括 `EditForm`）都支持任意属性。 与某个组件参数不匹配的所有属性都将添加到呈现的 HTML 元素中。

输入组件为编辑时验证以及更改其 CSS 类以反映字段状态提供默认行为。 某些组件包含有用的分析逻辑。 例如，`InputDate` 和 `InputNumber` 通过将无法分析的值注册为验证错误，以恰当的方式来处理它们。 可接受 Null 值的类型也支持目标字段的为 Null 性（例如，`int?`）。

下面的 `Starship` 类型使用比之前的 `ExampleModel` 更大的属性和数据注释集来定义验证逻辑：

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

在上面的示例中，`Description` 是可选的，因为不存在任何数据注释。

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

`EditForm` 创建一个 `EditContext` 作为[级联值](xref:blazor/components#cascading-values-and-parameters)来跟踪有关编辑过程的元数据，其中包括已修改的字段和当前的验证消息。 `EditForm` 还为有效和无效的提交提供便捷的事件（`OnValidSubmit`、`OnInvalidSubmit`）。 或者，使用 `OnSubmit` 触发验证并使用自定义验证代码检查字段值。

如下示例中：

* 选择“提交”按钮时，将运行 `HandleSubmit` 方法  。
* 使用窗体的 `EditContext` 验证窗体。
* 通过将 `EditContext` 传递给 `ServerValidate` 方法来进一步验证窗体，该方法会调用服务器上的 Web API 终结点（*未显示*）。
* 通过检查 `isValid` 获得客户端和服务器端验证的结果，并根据该结果运行其他代码。

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

使用 `InputText` 组件创建一个使用 `input` 事件而不是 `change` 事件的自定义组件。

使用以下标记创建一个组件，并像使用 `InputText` 一样使用该组件：

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

使用窗体中的单选按钮时，数据绑定的处理方式与其他元素不同，因为单选按钮是作为一个组进行计算的。 每个单选按钮的值是固定的，但单选按钮组的值是所选单选按钮的值。 以下示例介绍如何：

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

以下 `EditForm` 使用前面的 `InputRadio` 组件来获取和验证用户的评级：

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

`DataAnnotationsValidator` 组件使用数据注释将验证支持附加到级联的 `EditContext`。 使用数据注释启用对验证的支持需要此显式手势。 若要使用不同于数据注释的验证系统，请用自定义实现替换 `DataAnnotationsValidator`。 可在以下参考源中检查 ASP.NET Core 的实现：[DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)。

Blazor 执行两种类型的验证：

* 当用户从某个字段中跳出时，将执行*字段验证*。 在字段验证期间，`DataAnnotationsValidator` 组件将报告的所有验证结果与该字段相关联。
* 当用户提交窗体时，将执行*模型验证*。 在模型验证期间，`DataAnnotationsValidator` 组件尝试根据验证结果报告的成员名称来确定字段。 与单个成员无关的验证结果将与模型而不是字段相关联。

### <a name="validation-summary-and-validation-message-components"></a>验证摘要和验证消息组件

`ValidationSummary` 组件用于汇总所有验证消息，这与[验证摘要标记帮助程序](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)类似：

```razor
<ValidationSummary />
```

使用 `Model` 参数输出特定模型的验证消息：
  
```razor
<ValidationSummary Model="@_starship" />
```

`ValidationMessage` 组件用于显示特定字段的验证消息，这与[验证消息标记帮助程序](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)类似。 使用 `For` 属性和一个为模型属性命名的 Lambda 表达式来指定要验证的字段：

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

`ValidationMessage` 和 `ValidationSummary` 组件支持任意属性。 与某个组件参数不匹配的所有属性都将添加到生成的 `<div>` 或 `<ul>` 元素中。

### <a name="custom-validation-attributes"></a>自定义验证属性

当使用[自定义验证属性](xref:mvc/models/validation#custom-attributes)时，为确保验证结果与字段正确关联，请在创建 <xref:System.ComponentModel.DataAnnotations.ValidationResult> 时传递验证上下文的 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>：

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor 数据注释验证包

[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 是一个使用 `DataAnnotationsValidator` 组件填补验证体验缺口的验证包。 该包目前处于*试验阶段*。

### <a name="compareproperty-attribute"></a>[CompareProperty] 属性

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 `DataAnnotationsValidator` 组件，因为它不会将验证结果与特定成员关联。 这可能会导致字段级验证的行为与提交时整个模型的验证行为不一致。 [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 试验性包引入了一个附加的验证属性 `ComparePropertyAttribute`，它可以克服这些限制  。 在 Blazor 应用中，`[CompareProperty]` 可直接替代 `[Compare]` 属性。

### <a name="nested-models-collection-types-and-complex-types"></a>嵌套模型、集合类型和复杂类型

Blazor 支持结合使用数据注释和内置的 `DataAnnotationsValidator` 来验证窗体输入。 但是，`DataAnnotationsValidator` 仅验证绑定到窗体的模型的顶级属性（不包括集合类型或复杂类型的属性）。

若要验证绑定模型的整个对象图（包括集合类型和复杂类型的属性），请使用试验性 [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 包提供的 `ObjectGraphDataAnnotationsValidator`  ：

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

用 `[ValidateComplexType]` 注释模型属性。 在以下模型类中，`ShipDescription` 类包含附加数据注释，用于在将模型绑定到窗体时进行验证：

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>基于窗体验证启用提交按钮

若要基于窗体验证启用和禁用提交按钮，请执行以下操作：

* 使用窗体的 `EditContext` 在初始化组件时分配模型。
* 在上下文的 `OnFieldChanged` 回调中验证窗体，以启用和禁用提交按钮。
* 解除挂接 `Dispose` 方法中的事件处理程序。 有关详细信息，请参阅 <xref:blazor/lifecycle#component-disposal-with-idisposable>。

```razor
@implements IDisposable

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
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

在上面的示例中，如果满足以下条件，则将 `_formInvalid` 设置为 `false`：

* 窗体已预加载有效的默认值。
* 你希望在加载窗体时启用提交按钮。

上述方法的副作用是在用户与任何一个字段进行交互后，`ValidationSummary` 组件都会填充无效的字段。 可通过以下方式之一解决此情况：

* 不在窗体上使用 `ValidationSummary` 组件。
* 选择提交按钮时，使 `ValidationSummary` 组件可见（例如，在 `HandleValidSubmit` 方法中）。

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
