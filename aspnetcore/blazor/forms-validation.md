---
title: ASP.NET Core Blazor 窗体和验证
author: guardrex
description: 了解如何在 Blazor 中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/04/2019
uid: blazor/forms-validation
ms.openlocfilehash: 4531ef44a7df3951f3bebdf88e597165fa75f06e
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310329"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor 窗体和验证

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

使用[数据批注](xref:mvc/models/validation)在 Blazor 中支持窗体和验证。

> [!NOTE]
> 每个预览版本都可能会更改窗体和验证方案。

下面`ExampleModel`的类型使用数据批注定义验证逻辑：

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

使用`EditForm`组件定义窗体。 下面的窗体演示典型的元素、组件和 Razor 代码：

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

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

* 窗体使用`ExampleModel`在类型中定义`name`的验证来验证字段中的用户输入。 模型在组件的`@code`块中创建，并保存在私有字段（`exampleModel`）中。 该字段将分配给`Model`该`<EditForm>`元素的属性。
* `DataAnnotationsValidator`组件使用数据批注附加验证支持。
* `ValidationSummary`组件汇总验证消息。
* `HandleValidSubmit`当窗体成功提交（通过验证）时触发。

可使用一组内置输入组件来接收和验证用户输入。 当输入发生更改和提交窗体时，将对其进行验证。 下表显示了可用的输入组件。

| 输入组件 | 呈现为&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

所有输入组件（包括`EditForm`）都支持任意属性。 与组件参数不匹配的任何属性将添加到呈现的 HTML 元素中。

输入组件提供默认行为，用于验证编辑和更改其 CSS 类以反映字段状态。 某些组件包含有用的分析逻辑。 例如，通过`InputDate`将`InputNumber`其注册为验证错误，并适当地处理这些值。 可以接受 null 值的类型还支持目标字段的为空性（例如`int?`）。

下面`Starship`的类型使用比前面`ExampleModel`更大的属性和数据批注集定义验证逻辑：

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

在前面的示例中`Description` ，是可选的，因为不存在任何数据批注。

以下窗体使用在`Starship`模型中定义的验证来验证用户输入：

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

将`EditForm`创建一个`EditContext`作为[级联值](xref:blazor/components#cascading-values-and-parameters)，用于跟踪有关编辑过程的元数据，包括已修改的字段和当前验证消息。 还为有效提交和无效提交（`OnValidSubmit`， `OnInvalidSubmit`）提供便利事件。 `EditForm` 或者，使用`OnSubmit`通过自定义验证代码触发验证和检查字段值。

## <a name="inputtext-based-on-the-input-event"></a>基于输入事件的 InputText

使用组件来创建`input`使用事件而不是`change`事件的自定义组件。 `InputText`

使用以下标记创建组件，并使用如下所示`InputText`的组件：

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

组件使用数据批注将验证支持附加到级联`EditContext`。 `DataAnnotationsValidator` 使用数据批注启用验证支持目前需要此显式手势，但我们要考虑使其成为可以重写的默认行为。 若要使用不同于数据批注的验证系统，请`DataAnnotationsValidator`将替换为自定义实现。 ASP.NET Core 实现可用于引用源中的检查：[DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs). *ASP.NET Core 实现将在预览发布期间进行快速更新。*

该`ValidationSummary`组件汇总了所有验证消息，这类似于[验证摘要标记帮助](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)程序。

组件显示特定字段的验证消息，这类似于[验证消息标记帮助器。](xref:mvc/views/working-with-forms#the-validation-message-tag-helper) `ValidationMessage` 使用`For`特性指定要验证的字段，并指定命名模型属性的 lambda 表达式：

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

`ValidationMessage` 和`ValidationSummary`组件支持任意属性。 不匹配组件参数的任何属性将添加到生成`<div>`的或`<ul>`元素。

### <a name="validation-of-complex-or-collection-type-properties"></a>验证复杂或集合类型属性

应用于模型属性的验证属性在提交表单时进行验证。 但是，在提交表单时，不会验证模型的集合或复杂数据类型的属性。 若要在此方案中遵守嵌套验证属性，请使用自定义验证组件。 有关示例，请参阅[aspnet/Samples GitHub 存储库中的 Blazor 验证示例](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)。
