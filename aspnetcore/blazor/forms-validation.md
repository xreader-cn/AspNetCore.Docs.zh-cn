---
title: ASP.NET Core Blazor 窗体和验证
author: guardrex
description: 了解如何在 Blazor 中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/forms-validation
ms.openlocfilehash: 0b2e38cdbd974a28960b917fb6b5ce370f8c4659
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030330"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="61f8e-103">ASP.NET Core Blazor 窗体和验证</span><span class="sxs-lookup"><span data-stu-id="61f8e-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="61f8e-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="61f8e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="61f8e-105">使用[数据批注](xref:mvc/models/validation)在 Blazor 中支持窗体和验证。</span><span class="sxs-lookup"><span data-stu-id="61f8e-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="61f8e-106">每个预览版本都可能会更改窗体和验证方案。</span><span class="sxs-lookup"><span data-stu-id="61f8e-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="61f8e-107">下面`ExampleModel`的类型使用数据批注定义验证逻辑:</span><span class="sxs-lookup"><span data-stu-id="61f8e-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="61f8e-108">使用`EditForm`组件定义窗体。</span><span class="sxs-lookup"><span data-stu-id="61f8e-108">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="61f8e-109">下面的窗体演示典型的元素、组件和 Razor 代码:</span><span class="sxs-lookup"><span data-stu-id="61f8e-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="61f8e-110">窗体使用`ExampleModel`在类型中定义`name`的验证来验证字段中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="61f8e-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="61f8e-111">模型在组件的`@code`块中创建, 并保存在私有字段 (`exampleModel`) 中。</span><span class="sxs-lookup"><span data-stu-id="61f8e-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="61f8e-112">该字段将分配给`Model`该`<EditForm>`元素的属性。</span><span class="sxs-lookup"><span data-stu-id="61f8e-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="61f8e-113">`DataAnnotationsValidator`组件使用数据批注附加验证支持。</span><span class="sxs-lookup"><span data-stu-id="61f8e-113">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="61f8e-114">`ValidationSummary`组件汇总验证消息。</span><span class="sxs-lookup"><span data-stu-id="61f8e-114">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="61f8e-115">`HandleValidSubmit`当窗体成功提交 (通过验证) 时触发。</span><span class="sxs-lookup"><span data-stu-id="61f8e-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="61f8e-116">可使用一组内置输入组件来接收和验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="61f8e-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="61f8e-117">当输入发生更改和提交窗体时, 将对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="61f8e-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="61f8e-118">下表显示了可用的输入组件。</span><span class="sxs-lookup"><span data-stu-id="61f8e-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="61f8e-119">输入组件</span><span class="sxs-lookup"><span data-stu-id="61f8e-119">Input component</span></span> | <span data-ttu-id="61f8e-120">呈现为&hellip;</span><span class="sxs-lookup"><span data-stu-id="61f8e-120">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="61f8e-121">所有输入组件 (包括`EditForm`) 都支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="61f8e-121">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="61f8e-122">与组件参数不匹配的任何属性将添加到呈现的 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="61f8e-122">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="61f8e-123">输入组件提供默认行为, 用于验证编辑和更改其 CSS 类以反映字段状态。</span><span class="sxs-lookup"><span data-stu-id="61f8e-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="61f8e-124">某些组件包含有用的分析逻辑。</span><span class="sxs-lookup"><span data-stu-id="61f8e-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="61f8e-125">例如, 通过`InputDate`将`InputNumber`其注册为验证错误, 并适当地处理这些值。</span><span class="sxs-lookup"><span data-stu-id="61f8e-125">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="61f8e-126">可以接受 null 值的类型还支持目标字段的为空性 (例如`int?`)。</span><span class="sxs-lookup"><span data-stu-id="61f8e-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="61f8e-127">下面`Starship`的类型使用比前面`ExampleModel`更大的属性和数据批注集定义验证逻辑:</span><span class="sxs-lookup"><span data-stu-id="61f8e-127">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="61f8e-128">在前面的示例中`Description` , 是可选的, 因为不存在任何数据批注。</span><span class="sxs-lookup"><span data-stu-id="61f8e-128">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="61f8e-129">以下窗体使用在`Starship`模型中定义的验证来验证用户输入:</span><span class="sxs-lookup"><span data-stu-id="61f8e-129">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="61f8e-130">将`EditForm`创建一个`EditContext`作为[级联值](xref:blazor/components#cascading-values-and-parameters), 用于跟踪有关编辑过程的元数据, 包括已修改的字段和当前验证消息。</span><span class="sxs-lookup"><span data-stu-id="61f8e-130">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="61f8e-131">还为有效提交和无效提交 (`OnValidSubmit`, `OnInvalidSubmit`) 提供便利事件。 `EditForm`</span><span class="sxs-lookup"><span data-stu-id="61f8e-131">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="61f8e-132">或者, 使用`OnSubmit`通过自定义验证代码触发验证和检查字段值。</span><span class="sxs-lookup"><span data-stu-id="61f8e-132">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="61f8e-133">组件使用数据批注将验证支持附加到级联`EditContext`。 `DataAnnotationsValidator`</span><span class="sxs-lookup"><span data-stu-id="61f8e-133">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="61f8e-134">使用数据批注启用验证支持目前需要此显式手势, 但我们要考虑使其成为可以重写的默认行为。</span><span class="sxs-lookup"><span data-stu-id="61f8e-134">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="61f8e-135">若要使用不同于数据批注的验证系统, 请`DataAnnotationsValidator`将替换为自定义实现。</span><span class="sxs-lookup"><span data-stu-id="61f8e-135">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="61f8e-136">ASP.NET Core 实现可用于引用源中的检查:[DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="61f8e-136">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="61f8e-137">*ASP.NET Core 实现将在预览发布期间进行快速更新。*</span><span class="sxs-lookup"><span data-stu-id="61f8e-137">*The ASP.NET Core implementation is subject to rapid updates during the preview release period.*</span></span>

<span data-ttu-id="61f8e-138">该`ValidationSummary`组件汇总了所有验证消息, 这类似于[验证摘要标记帮助](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)程序。</span><span class="sxs-lookup"><span data-stu-id="61f8e-138">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="61f8e-139">组件显示特定字段的验证消息, 这类似于[验证消息标记帮助器。](xref:mvc/views/working-with-forms#the-validation-message-tag-helper) `ValidationMessage`</span><span class="sxs-lookup"><span data-stu-id="61f8e-139">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="61f8e-140">使用`For`特性指定要验证的字段, 并指定命名模型属性的 lambda 表达式:</span><span class="sxs-lookup"><span data-stu-id="61f8e-140">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="61f8e-141">`ValidationMessage` 和`ValidationSummary`组件支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="61f8e-141">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="61f8e-142">不匹配组件参数的任何属性将添加到生成`<div>`的或`<ul>`元素。</span><span class="sxs-lookup"><span data-stu-id="61f8e-142">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>
