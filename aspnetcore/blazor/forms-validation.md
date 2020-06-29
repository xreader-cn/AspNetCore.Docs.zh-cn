---
title: ASP.NET Core Blazor 窗体和验证
author: guardrex
description: 了解如何在 Blazor 中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 588a24f7850c35bcbadc1c86edc61b23cc7a913e
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242662"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor 窗体和验证

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

在 Blazor 中，使用[数据注释](xref:mvc/models/validation)支持窗体和验证。

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

窗体是使用 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 组件定义的。 以下窗体展示了典型的元素、组件和 Razor 代码：

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

在上面的示例中：

* 该窗体使用 `ExampleModel` 类型中定义的验证来验证 `name` 字段中的用户输入。 该模型在组件的 `@code` 块中创建，并保存在私有字段 (`exampleModel`) 中。 该字段分配给 `<EditForm>` 元素的 `Model` 属性。
* <xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件的 `@bind-Value` 进行以下绑定：
  * 将模型属性 (`exampleModel.Name`) 绑定到 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件的 `Value` 属性。 有关属性绑定的详细信息，请参阅 <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>。
  * 将更改事件委托绑定到 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件的 `ValueChanged` 属性。
* <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件使用数据注释附加验证支持。
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件汇总验证消息。
* 窗体成功提交（通过验证）时触发 `HandleValidSubmit`。

可使用一组内置的输入组件来接收和验证用户输入。 当更改输入和提交窗体时，将验证输入。 下表显示了可用的输入组件。

| 输入组件 | 呈现为&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

所有输入组件（包括 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>）都支持任意属性。 与某个组件参数不匹配的所有属性都将添加到呈现的 HTML 元素中。

输入组件为编辑时验证以及更改其 CSS 类以反映字段状态提供默认行为。 某些组件包含有用的分析逻辑。 例如，<xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 和 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 通过将无法分析的值注册为验证错误，以恰当的方式来处理它们。 可接受 Null 值的类型也支持目标字段的为 Null 性（例如，`int?`）。

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

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
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
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<xref:Microsoft.AspNetCore.Components.Forms.EditForm> 创建一个 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 作为[级联值](xref:blazor/components/cascading-values-and-parameters)来跟踪有关编辑过程的元数据，其中包括已修改的字段和当前的验证消息。 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 还为有效和无效的提交提供便捷的事件（<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>、<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>）。 或者，使用 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> 触发验证并使用自定义验证代码检查字段值。

如下示例中：

* 选择 `Submit` 按钮时，将运行 `HandleSubmit` 方法。
* 使用窗体的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 验证窗体。
* 通过将 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 传递给 `ServerValidate` 方法来进一步验证窗体，该方法会调用服务器上的 Web API 终结点（*未显示*）。
* 通过检查 `isValid` 获得客户端和服务器端验证的结果，并根据该结果运行其他代码。

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

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

使用 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件创建一个使用 `input` 事件而不是 `change` 事件的自定义组件。

在下面的示例中，`CustomInputText` 组件继承框架的 `InputText` 组件，并将事件绑定 (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) 设置为 `oninput` 事件。

`Shared/CustomInputText.razor`：

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

`CustomInputText` 组件可在任何使用 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 的位置使用：

`Pages/TestForm.razor`：

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
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

以下 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 使用前面的 `InputRadio` 组件来获取和验证用户的评级：

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

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

<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件使用数据注释将验证支持附加到级联的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>。 使用数据注释启用对验证的支持需要此显式手势。 若要使用不同于数据注释的验证系统，请用自定义实现替换 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>。 可在以下参考源中检查 ASP.NET Core 的实现[`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)： 前面的参考源链接提供了来自存储库 `master` 分支的代码，该分支表示产品单元当前对 ASP.NET Core 下一版本的开发。 若要为其他版本选择分支，请使用 GitHub 分支选择器（例如 `release/3.1`）。

Blazor 执行两种类型的验证：

* 当用户从某个字段中跳出时，将执行*字段验证*。 在字段验证期间，<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件将报告的所有验证结果与该字段相关联。
* 当用户提交窗体时，将执行*模型验证*。 在模型验证期间，<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件尝试根据验证结果报告的成员名称来确定字段。 与单个成员无关的验证结果将与模型而不是字段相关联。

### <a name="validation-summary-and-validation-message-components"></a>验证摘要和验证消息组件

<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件用于汇总所有验证消息，这与[验证摘要标记帮助程序](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)类似：

```razor
<ValidationSummary />
```

使用 `Model` 参数输出特定模型的验证消息：
  
```razor
<ValidationSummary Model="@starship" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 组件用于显示特定字段的验证消息，这与[验证消息标记帮助程序](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)类似。 使用 `For` 属性和一个为模型属性命名的 Lambda 表达式来指定要验证的字段：

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 和 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件支持任意属性。 与某个组件参数不匹配的所有属性都将添加到生成的 `<div>` 或 `<ul>` 元素中。

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

### <a name="blazor-data-annotations-validation-package"></a>Blazor 数据注释验证包

[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 是使用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件填补验证经验空白的包。 该包目前处于*试验阶段*。

### <a name="compareproperty-attribute"></a>[CompareProperty] 属性

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件，因为它不会将验证结果与特定成员关联。 这可能会导致字段级验证的行为与提交时整个模型的验证行为不一致。 [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 试验性包引入了一个附加的验证属性 `ComparePropertyAttribute`，它可以克服这些限制。 在 Blazor 应用中，`[CompareProperty]` 可直接替代 [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 特性。

### <a name="nested-models-collection-types-and-complex-types"></a>嵌套模型、集合类型和复杂类型

Blazor 支持结合使用数据注释和内置的 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 来验证窗体输入。 但是，<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 仅验证绑定到窗体的模型的顶级属性（不包括集合类型或复杂类型的属性）。

若要验证绑定模型的整个对象图（包括集合类型和复杂类型的属性），请使用试验性 [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 包提供的 `ObjectGraphDataAnnotationsValidator`：

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

用 `[ValidateComplexType]` 注释模型属性。 在以下模型类中，`ShipDescription` 类包含附加数据注释，用于在将模型绑定到窗体时进行验证：

`Starship.cs`：

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

`ShipDescription.cs`：

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

* 使用窗体的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 在初始化组件时分配模型。
* 在上下文的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 回调中验证窗体，以启用和禁用提交按钮。
* 解除挂接 `Dispose` 方法中的事件处理程序。 有关详细信息，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

在上面的示例中，如果满足以下条件，则将 `formInvalid` 设置为 `false`：

* 窗体已预加载有效的默认值。
* 你希望在加载窗体时启用提交按钮。

上述方法的副作用是在用户与任何一个字段进行交互后，<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件都会填充无效的字段。 可通过以下方式之一解决此情况：

* 不在窗体上使用 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件。
* 选择提交按钮时，使 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件可见（例如，在 `HandleValidSubmit` 方法中）。

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a>疑难解答

> InvalidOperationException：EditForm 需要 Model 参数或 EditContext 参数，但不能同时需要这两个参数。

确认 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 是否有 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> 或 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>。

在将 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> 分配到窗体时，请确认模型类型是否已实例化，如下面的示例所示：

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
