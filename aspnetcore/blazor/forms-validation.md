---
title: ASP.NET Core Blazor 窗体和验证
author: guardrex
description: 了解如何在 Blazor 中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: blazor/forms-validation
ms.openlocfilehash: 6dcc36c5133367493b476655dbdf73b75db9d168
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905736"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor 窗体和验证

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

使用[数据批注](xref:mvc/models/validation)在 Blazor 中支持窗体和验证。

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

```cshtml
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

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a>验证支持

`DataAnnotationsValidator` 组件使用数据批注将验证支持附加到级联 `EditContext`。 使用数据批注启用验证支持需要此显式手势。 若要使用不同于数据批注的验证系统，请将 `DataAnnotationsValidator` 替换为自定义实现。 ASP.NET Core 实现可在引用源： [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)中进行检查。

`ValidationSummary` 组件汇总了所有验证消息，这类似于[验证摘要标记帮助器](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)。

`ValidationMessage` 组件显示特定字段的验证消息，这类似于[验证消息标记帮助器](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)。 使用 `For` 特性指定验证字段，并使用命名模型属性的 lambda 表达式指定：

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

`ValidationMessage` 和 `ValidationSummary` 组件支持任意属性。 与组件参数不匹配的任何属性将添加到生成的 `<div>` 或 `<ul>` 元素。

::: moniker range=">= aspnetcore-3.1"

**AspNetCore. Blazor. DataAnnotations 包**

[AspNetCore. Blazor. DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)是使用 `DataAnnotationsValidator` 组件填充验证体验缺口的包。 包当前是*实验*性的，我们计划在未来版本中将这些方案添加到 ASP.NET Core 框架中。

`DataAnnotationsValidator` 组件不会验证验证模型上的复杂属性的子属性。 集合类型的项不会进行验证。 为了验证这些类型，`Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` 包引入了 `ValidateComplexType` 验证特性，该特性与 `ObjectGraphDataAnnotationsValidator` 组件一起使用。 有关所使用的这些类型的示例，请参阅[aspnet/Samples GitHub 存储库中的 Blazor 验证示例](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)。

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 `DataAnnotationsValidator` 组件。 `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` 包引入了一个附加的验证特性 `ComparePropertyAttribute`，用于解决这些限制。 在 Blazor 应用中，`ComparePropertyAttribute` 是 `CompareAttribute`的直接替换。 有关详细信息，请参阅[CompareAttribute With OnValidSubmit EditForm （aspnet/AspNetCore \#10643）已忽略](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748)。

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a>验证复杂或集合类型属性

应用于模型属性的验证属性在提交表单时进行验证。 但是，在由 `DataAnnotationsValidator` 组件提交窗体时，不会验证模型的集合或复杂数据类型的属性。 若要在此方案中遵守嵌套验证属性，请使用自定义验证组件。 有关示例，请参阅[aspnet/Samples GitHub 存储库中的 Blazor 验证示例](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)。

::: moniker-end
