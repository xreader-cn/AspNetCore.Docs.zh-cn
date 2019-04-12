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
# <a name="razor-components-forms-and-validation"></a><span data-ttu-id="d6c90-103">Razor 组件窗体和验证</span><span class="sxs-lookup"><span data-stu-id="d6c90-103">Razor Components forms and validation</span></span>

<span data-ttu-id="d6c90-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d6c90-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d6c90-105">使用 Razor 组件支持窗体和验证[数据批注](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="d6c90-105">Forms and validation are supported in Razor Components using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="d6c90-106">窗体和验证方案是可能会更改每个预览版本。</span><span class="sxs-lookup"><span data-stu-id="d6c90-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="d6c90-107">以下`ExampleModel`类型定义验证逻辑使用数据注释：</span><span class="sxs-lookup"><span data-stu-id="d6c90-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="d6c90-108">使用定义窗体`<EditForm>`组件。</span><span class="sxs-lookup"><span data-stu-id="d6c90-108">A form is defined using the `<EditForm>` component.</span></span> <span data-ttu-id="d6c90-109">以下窗体演示了典型元素、 组件和 Razor 代码：</span><span class="sxs-lookup"><span data-stu-id="d6c90-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="d6c90-110">在窗体验证中的用户输入`name`字段使用中定义的验证`ExampleModel`类型。</span><span class="sxs-lookup"><span data-stu-id="d6c90-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="d6c90-111">在组件中创建模型`@functions`阻止和保存在私有字段中 (`exampleModel`)。</span><span class="sxs-lookup"><span data-stu-id="d6c90-111">The model is created in the component's `@functions` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="d6c90-112">该字段分配给`Model`属性的`<EditForm>`。</span><span class="sxs-lookup"><span data-stu-id="d6c90-112">The field is assigned to the `Model` attribute of the `<EditForm>`.</span></span>
* <span data-ttu-id="d6c90-113">数据批注验证程序组件 (`<DataAnnotationsValidator>`) 将附加使用数据批注验证支持。</span><span class="sxs-lookup"><span data-stu-id="d6c90-113">The Data Annotations Validator component (`<DataAnnotationsValidator>`) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="d6c90-114">验证摘要组件 (`<ValidationSummary>`) 总结了验证消息。</span><span class="sxs-lookup"><span data-stu-id="d6c90-114">The Validation Summary component (`<ValidationSummary>`) summarizes validation messages.</span></span>
* `HandleValidSubmit` <span data-ttu-id="d6c90-115">当窗体已成功提交 （通过验证） 时触发。</span><span class="sxs-lookup"><span data-stu-id="d6c90-115">is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="d6c90-116">一组内置的输入组件是可用于接收和验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="d6c90-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="d6c90-117">当它们正在更改和提交窗体时，输入进行验证。</span><span class="sxs-lookup"><span data-stu-id="d6c90-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="d6c90-118">下表中显示可用的输入的组件。</span><span class="sxs-lookup"><span data-stu-id="d6c90-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="d6c90-119">输入的组件</span><span class="sxs-lookup"><span data-stu-id="d6c90-119">Input component</span></span>   | <span data-ttu-id="d6c90-120">呈现为&hellip;</span><span class="sxs-lookup"><span data-stu-id="d6c90-120">Rendered as&hellip;</span></span>       |
| ----------------- | ------------------------- |
| `<InputText>`     | `<input>`                 |
| `<InputTextArea>` | `<textarea>`              |
| `<InputSelect>`   | `<select>`                |
| `<InputNumber>`   | `<input type="number">`   |
| `<InputCheckbox>` | `<input type="checkbox">` |
| `<InputDate>`     | `<input type="date">`     |

<span data-ttu-id="d6c90-121">输入的组件提供用于验证上编辑和更改以反映的字段状态其 CSS 类的默认行为。</span><span class="sxs-lookup"><span data-stu-id="d6c90-121">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="d6c90-122">某些组件包括有用的分析逻辑。</span><span class="sxs-lookup"><span data-stu-id="d6c90-122">Some components include useful parsing logic.</span></span> <span data-ttu-id="d6c90-123">例如，`<InputDate>`和`<InputNumber>`通过其注册为验证错误的适当地处理无法分析值。</span><span class="sxs-lookup"><span data-stu-id="d6c90-123">For example, `<InputDate>` and `<InputNumber>` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="d6c90-124">可接受 null 值的类型还支持的目标字段为空性 (例如， `int?`)。</span><span class="sxs-lookup"><span data-stu-id="d6c90-124">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="d6c90-125">以下`Starship`类型定义验证逻辑使用更大的一组属性和[数据批注](xref:mvc/models/validation)比早期版本`ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="d6c90-125">The following `Starship` type defines validation logic using a larger set of properties and [data annotations](xref:mvc/models/validation) than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="d6c90-126">以下窗体验证用户输入使用中定义的验证`Starship`模型：</span><span class="sxs-lookup"><span data-stu-id="d6c90-126">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="d6c90-127">`<EditForm>`创建`EditContext`作为[级联值](xref:razor-components/components#cascading-values-and-parameters)用于跟踪有关编辑过程中，包括哪些字段已被修改的元数据和当前的验证消息。</span><span class="sxs-lookup"><span data-stu-id="d6c90-127">The `<EditForm>` creates an `EditContext` as a [cascading value](xref:razor-components/components#cascading-values-and-parameters) that tracks metadata about the edit process, including what fields have been modified and the current validation messages.</span></span> <span data-ttu-id="d6c90-128">`<EditForm>`还提供了方便的事件的有效和无效提交 (`OnValidSubmit`， `OnInvalidSubmit`)。</span><span class="sxs-lookup"><span data-stu-id="d6c90-128">The `<EditForm>` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="d6c90-129">或者，使用`OnSubmit`触发验证并检查与自定义验证代码的字段值。</span><span class="sxs-lookup"><span data-stu-id="d6c90-129">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="d6c90-130">数据批注验证程序组件 (`<DataAnnotationsValidator>`) 将附加验证支持使用级联的数据批注`EditContext`。</span><span class="sxs-lookup"><span data-stu-id="d6c90-130">The Data Annotations Validator component (`<DataAnnotationsValidator>`) attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="d6c90-131">启用对当前使用数据批注验证的支持需要此显式手势，但我们正考虑使此然后，可以替代默认行为。</span><span class="sxs-lookup"><span data-stu-id="d6c90-131">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="d6c90-132">若要使用不同的验证系统要比数据批注，请将数据批注验证程序替换为自定义实现。</span><span class="sxs-lookup"><span data-stu-id="d6c90-132">To use a different validation system than data annotations, replace the Data Annotations Validator with a custom implementation.</span></span> <span data-ttu-id="d6c90-133">ASP.NET Core 实现现在可供引用源中的检查：[DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="d6c90-133">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> *<span data-ttu-id="d6c90-134">ASP.NET Core 实现是将来可能会快速预览版本期间更新。</span><span class="sxs-lookup"><span data-stu-id="d6c90-134">The ASP.NET Core implementation is subject to rapid updates during the preview release period.</span></span>*

<span data-ttu-id="d6c90-135">验证摘要组件 (`<ValidationSummary>`) 汇总了所有验证消息，这类似于[验证摘要标记帮助程序](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="d6c90-135">The Validation Summary component (`<ValidationSummary>`) summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="d6c90-136">验证消息组件 (`<ValidationMessage>`) 显示验证消息的特定字段中，这类似于[验证消息标记帮助程序](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="d6c90-136">The Validation Message component (`<ValidationMessage>`) displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="d6c90-137">指定具有的验证字段`For`属性和命名的模型属性的 lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="d6c90-137">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

> [!NOTE]
> <span data-ttu-id="d6c90-138">内置的输入的组件有限制，我们希望解决将来的版本。</span><span class="sxs-lookup"><span data-stu-id="d6c90-138">Built-in input components have limitations that we expect to resolve in future releases.</span></span> <span data-ttu-id="d6c90-139">例如，不能指定任意属性上生成`<input>`标记。</span><span class="sxs-lookup"><span data-stu-id="d6c90-139">For example, you can't specify arbitrary attributes on the generated `<input>` tags.</span></span> <span data-ttu-id="d6c90-140">构建您自己的组件子类来处理方案，不可用。</span><span class="sxs-lookup"><span data-stu-id="d6c90-140">Build your own component subclasses to handle unavailable scenarios.</span></span>
