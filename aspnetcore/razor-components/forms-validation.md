---
title: Razor 组件窗体和验证
author: guardrex
description: 了解如何在 Razor 组件中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/forms-validation
ms.openlocfilehash: a4ed75efc6b5a733ce4ff4e82f354a8e2fd48500
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515424"
---
# <a name="razor-components-forms-and-validation"></a>Razor 组件窗体和验证

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

使用 Razor 组件支持窗体和验证[数据批注](xref:mvc/models/validation)。

> [!NOTE]
> 窗体和验证方案是可能会更改每个预览版本。

以下`ExampleModel`类型定义验证逻辑使用数据注释：

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

使用定义窗体`<EditForm>`组件。 以下窗体演示了典型元素、 组件和 Razor 代码：

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" bind-Value="@exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@functions {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

* 在窗体验证中的用户输入`name`字段使用中定义的验证`ExampleModel`类型。 在组件中创建模型`@functions`阻止和保存在私有字段中 (`exampleModel`)。 该字段分配给`Model`属性的`<EditForm>`。
* 数据批注验证程序组件 (`<DataAnnotationsValidator>`) 将附加使用数据批注验证支持。
* 验证摘要组件 (`<ValidationSummary>`) 总结了验证消息。
* `HandleValidSubmit` 当窗体已成功提交 （通过验证） 时触发。

一组内置的输入组件是可用于接收和验证用户输入。 当它们正在更改和提交窗体时，输入进行验证。 下表中显示可用的输入的组件。

| 输入的组件   | 呈现为&hellip;       |
| ----------------- | ------------------------- |
| `<InputText>`     | `<input>`                 |
| `<InputTextArea>` | `<textarea>`              |
| `<InputSelect>`   | `<select>`                |
| `<InputNumber>`   | `<input type="number">`   |
| `<InputCheckbox>` | `<input type="checkbox">` |
| `<InputDate>`     | `<input type="date">`     |

输入的组件提供用于验证上编辑和更改以反映的字段状态其 CSS 类的默认行为。 某些组件包括有用的分析逻辑。 例如，`<InputDate>`和`<InputNumber>`通过其注册为验证错误的适当地处理无法分析值。 可接受 null 值的类型还支持的目标字段为空性 (例如， `int?`)。

以下`Starship`类型定义验证逻辑使用更大的一组属性和[数据批注](xref:mvc/models/validation)比早期版本`ExampleModel`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, 
        ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    // Optional (no data annotations)
    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "Form disallowed for unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

以下窗体验证用户输入使用中定义的验证`Starship`模型：

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" bind-Value="@starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea Id="description" bind-Value="@starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" bind-Value="@starship.Classification">
            <option value"">Select classification ...</option>
            <option value="Defense">Defense</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            bind-Value="@starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" bind-Value="@starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate Id="productionDate" bind-Value="@starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="http://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@functions {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

`<EditForm>`创建`EditContext`作为[级联值](xref:razor-components/components#cascading-values-and-parameters)用于跟踪有关编辑过程中，包括哪些字段已被修改的元数据和当前的验证消息。 `<EditForm>`还提供了方便的事件的有效和无效提交 (`OnValidSubmit`， `OnInvalidSubmit`)。 或者，使用`OnSubmit`触发验证并检查与自定义验证代码的字段值。

数据批注验证程序组件 (`<DataAnnotationsValidator>`) 将附加验证支持使用级联的数据批注`EditContext`。 启用对当前使用数据批注验证的支持需要此显式手势，但我们正考虑使此然后，可以替代默认行为。 若要使用不同的验证系统要比数据批注，请将数据批注验证程序替换为自定义实现。 ASP.NET Core 实现现在可供引用源中的检查：[DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs)。 *ASP.NET Core 实现是将来可能会快速预览版本期间更新。*

验证摘要组件 (`<ValidationSummary>`) 汇总了所有验证消息，这类似于[验证摘要标记帮助程序](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)。

验证消息组件 (`<ValidationMessage>`) 显示验证消息的特定字段中，这类似于[验证消息标记帮助程序](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)。 指定具有的验证字段`For`属性和命名的模型属性的 lambda 表达式：

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

> [!NOTE]
> 内置的输入的组件有限制，我们希望解决将来的版本。 例如，不能指定任意属性上生成`<input>`标记。 构建您自己的组件子类来处理方案，不可用。
