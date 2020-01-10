---
title: ASP.NET Core Blazor 窗体和验证
author: guardrex
description: 了解如何在 Blazor中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/forms-validation
ms.openlocfilehash: a94a433f26e451bbadc73615e502e46d273f05c2
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828134"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a>ASP.NET Core Blazor 窗体和验证

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

使用[数据批注](xref:mvc/models/validation)Blazor 支持窗体和验证。

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

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
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

* 窗体使用 `ExampleModel` 类型中定义的验证来验证 `name` 字段中的用户输入。 该模型是在组件的 `@code` 块中创建的，并保存在私有字段（`exampleModel`）中。 该字段将分配给 `<EditForm>` 元素的 `Model` 特性。
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

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
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

`EditForm` 创建一个 `EditContext` 作为[级联值](xref:blazor/components#cascading-values-and-parameters)，用于跟踪有关编辑过程的元数据，包括已修改的字段和当前验证消息。 `EditForm` 还为有效提交和无效提交（`OnValidSubmit`、`OnInvalidSubmit`）提供便利事件。 或者，使用 `OnSubmit` 使用自定义验证代码来触发验证和检查字段值。

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
<ValidationSummary Model="@starship" />
```

`ValidationMessage` 组件显示特定字段的验证消息，这类似于[验证消息标记帮助器](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)。 使用 `For` 特性指定验证字段，并使用命名模型属性的 lambda 表达式指定：

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
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

::: moniker range=">= aspnetcore-3.1"

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor 数据批注验证包

[AspNetCore.Blazor。DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)是使用 `DataAnnotationsValidator` 组件填充验证体验缺口的包。 包当前正在*试验*。

### <a name="compareproperty-attribute"></a>[CompareProperty] 属性

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 `DataAnnotationsValidator` 组件。 [AspNetCore.Blazor。DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *实验*包引入了一个附加的验证特性 `ComparePropertyAttribute`，该特性围绕这些限制。 在 Blazor 应用中，`[CompareProperty]` 是 `[Compare]` 属性的直接替换。 有关详细信息，请参阅[CompareAttribute With OnValidSubmit EditForm （dotnet/AspNetCore #10643）已忽略](https://github.com/dotnet/AspNetCore/issues/10643#issuecomment-543909748)。

### <a name="nested-models-collection-types-and-complex-types"></a>嵌套模型、集合类型和复杂类型

Blazor 提供对通过内置 `DataAnnotationsValidator`使用数据批注验证窗体输入的支持。 但是，`DataAnnotationsValidator` 仅验证绑定到不是集合或复杂类型属性的窗体的模型的顶级属性。

若要验证绑定模型的整个对象关系图，包括集合和复杂类型属性，请使用BlazorAspNetCore 提供的 `ObjectGraphDataAnnotationsValidator` 。 [DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)包：

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
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

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a>验证复杂或集合类型属性

应用于模型属性的验证属性在提交表单时进行验证。 但是，在由 `DataAnnotationsValidator` 组件提交窗体时，不会验证模型的集合或复杂数据类型的属性。 若要在此方案中遵守嵌套验证属性，请使用自定义验证组件。 有关示例，请参阅[Blazor 验证示例（aspnet/示例）](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)。

::: moniker-end
