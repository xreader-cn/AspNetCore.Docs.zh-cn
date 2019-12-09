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
ms.openlocfilehash: f4c1845ee4b6ff9274b7117167367ccdd9f36c12
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943688"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a><span data-ttu-id="e49da-103">ASP.NET Core Blazor 窗体和验证</span><span class="sxs-lookup"><span data-stu-id="e49da-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="e49da-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e49da-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e49da-105">使用[数据批注](xref:mvc/models/validation)Blazor 支持窗体和验证。</span><span class="sxs-lookup"><span data-stu-id="e49da-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="e49da-106">以下 `ExampleModel` 类型使用数据批注定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="e49da-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="e49da-107">使用 `EditForm` 组件定义窗体。</span><span class="sxs-lookup"><span data-stu-id="e49da-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="e49da-108">下面的窗体演示典型的元素、组件和 Razor 代码：</span><span class="sxs-lookup"><span data-stu-id="e49da-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="e49da-109">窗体使用 `ExampleModel` 类型中定义的验证来验证 `name` 字段中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="e49da-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="e49da-110">该模型是在组件的 `@code` 块中创建的，并保存在私有字段（`exampleModel`）中。</span><span class="sxs-lookup"><span data-stu-id="e49da-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="e49da-111">该字段将分配给 `<EditForm>` 元素的 `Model` 特性。</span><span class="sxs-lookup"><span data-stu-id="e49da-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="e49da-112">`DataAnnotationsValidator` 组件使用数据批注附加验证支持。</span><span class="sxs-lookup"><span data-stu-id="e49da-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="e49da-113">`ValidationSummary` 组件汇总验证消息。</span><span class="sxs-lookup"><span data-stu-id="e49da-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="e49da-114">当窗体成功提交（通过验证）时，将触发 `HandleValidSubmit`。</span><span class="sxs-lookup"><span data-stu-id="e49da-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="e49da-115">可使用一组内置输入组件来接收和验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="e49da-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="e49da-116">当输入发生更改和提交窗体时，将对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="e49da-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="e49da-117">下表显示了可用的输入组件。</span><span class="sxs-lookup"><span data-stu-id="e49da-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="e49da-118">输入组件</span><span class="sxs-lookup"><span data-stu-id="e49da-118">Input component</span></span> | <span data-ttu-id="e49da-119">呈现为&hellip;</span><span class="sxs-lookup"><span data-stu-id="e49da-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="e49da-120">所有输入组件（包括 `EditForm`）都支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="e49da-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="e49da-121">与组件参数不匹配的任何属性将添加到呈现的 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="e49da-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="e49da-122">输入组件提供默认行为，用于验证编辑和更改其 CSS 类以反映字段状态。</span><span class="sxs-lookup"><span data-stu-id="e49da-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="e49da-123">某些组件包含有用的分析逻辑。</span><span class="sxs-lookup"><span data-stu-id="e49da-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="e49da-124">例如，`InputDate` 和 `InputNumber` 通过将其注册为验证错误来适当地处理无法分析的值。</span><span class="sxs-lookup"><span data-stu-id="e49da-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="e49da-125">可以接受 null 值的类型还支持目标字段的为空性（例如 `int?`）。</span><span class="sxs-lookup"><span data-stu-id="e49da-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="e49da-126">以下 `Starship` 类型使用比之前 `ExampleModel`更多的属性和数据批注集定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="e49da-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="e49da-127">在前面的示例中，`Description` 是可选的，因为不存在任何数据批注。</span><span class="sxs-lookup"><span data-stu-id="e49da-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="e49da-128">以下窗体使用 `Starship` 模型中定义的验证来验证用户输入：</span><span class="sxs-lookup"><span data-stu-id="e49da-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="e49da-129">`EditForm` 创建一个 `EditContext` 作为[级联值](xref:blazor/components#cascading-values-and-parameters)，用于跟踪有关编辑过程的元数据，包括已修改的字段和当前验证消息。</span><span class="sxs-lookup"><span data-stu-id="e49da-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="e49da-130">`EditForm` 还为有效提交和无效提交（`OnValidSubmit`、`OnInvalidSubmit`）提供便利事件。</span><span class="sxs-lookup"><span data-stu-id="e49da-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="e49da-131">或者，使用 `OnSubmit` 使用自定义验证代码来触发验证和检查字段值。</span><span class="sxs-lookup"><span data-stu-id="e49da-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="e49da-132">基于输入事件的 InputText</span><span class="sxs-lookup"><span data-stu-id="e49da-132">InputText based on the input event</span></span>

<span data-ttu-id="e49da-133">使用 `InputText` 组件创建使用 `input` 事件而不是 `change` 事件的自定义组件。</span><span class="sxs-lookup"><span data-stu-id="e49da-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="e49da-134">使用以下标记创建组件，并使用组件，就像使用 `InputText` 一样：</span><span class="sxs-lookup"><span data-stu-id="e49da-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="e49da-135">验证支持</span><span class="sxs-lookup"><span data-stu-id="e49da-135">Validation support</span></span>

<span data-ttu-id="e49da-136">`DataAnnotationsValidator` 组件使用数据批注将验证支持附加到级联 `EditContext`。</span><span class="sxs-lookup"><span data-stu-id="e49da-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="e49da-137">使用数据批注启用验证支持需要此显式手势。</span><span class="sxs-lookup"><span data-stu-id="e49da-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="e49da-138">若要使用不同于数据批注的验证系统，请将 `DataAnnotationsValidator` 替换为自定义实现。</span><span class="sxs-lookup"><span data-stu-id="e49da-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="e49da-139">ASP.NET Core 实现可在引用源： [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)中进行检查。</span><span class="sxs-lookup"><span data-stu-id="e49da-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="e49da-140"> 执行两种类型的验证：</span><span class="sxs-lookup"><span data-stu-id="e49da-140"> performs two types of validation:</span></span>

* <span data-ttu-id="e49da-141">当用户在字段外切换时，将执行*字段验证*。</span><span class="sxs-lookup"><span data-stu-id="e49da-141">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="e49da-142">在字段验证过程中，`DataAnnotationsValidator` 组件将所有报告的验证结果与该字段相关联。</span><span class="sxs-lookup"><span data-stu-id="e49da-142">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="e49da-143">当用户提交窗体时，将执行*模型验证*。</span><span class="sxs-lookup"><span data-stu-id="e49da-143">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="e49da-144">在模型验证过程中，`DataAnnotationsValidator` 组件尝试基于验证结果报告的成员名称确定字段。</span><span class="sxs-lookup"><span data-stu-id="e49da-144">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="e49da-145">与单个成员无关的验证结果与模型关联，而不是与字段关联。</span><span class="sxs-lookup"><span data-stu-id="e49da-145">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="e49da-146">验证摘要和验证消息组件</span><span class="sxs-lookup"><span data-stu-id="e49da-146">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="e49da-147">`ValidationSummary` 组件汇总了与[验证摘要标记帮助](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)程序类似的所有验证消息：</span><span class="sxs-lookup"><span data-stu-id="e49da-147">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="e49da-148">使用 `Model` 参数输出特定模型的验证消息：</span><span class="sxs-lookup"><span data-stu-id="e49da-148">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="e49da-149">`ValidationMessage` 组件显示特定字段的验证消息，这类似于[验证消息标记帮助器](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="e49da-149">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="e49da-150">使用 `For` 特性指定验证字段，并使用命名模型属性的 lambda 表达式指定：</span><span class="sxs-lookup"><span data-stu-id="e49da-150">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="e49da-151">`ValidationMessage` 和 `ValidationSummary` 组件支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="e49da-151">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="e49da-152">与组件参数不匹配的任何属性将添加到生成的 `<div>` 或 `<ul>` 元素。</span><span class="sxs-lookup"><span data-stu-id="e49da-152">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="e49da-153">自定义验证特性</span><span class="sxs-lookup"><span data-stu-id="e49da-153">Custom validation attributes</span></span>

<span data-ttu-id="e49da-154">若要确保在使用[自定义验证属性](xref:mvc/models/validation#custom-attributes)时验证结果与字段正确关联，请在创建 <xref:System.ComponentModel.DataAnnotations.ValidationResult>时传递验证上下文的 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>：</span><span class="sxs-lookup"><span data-stu-id="e49da-154">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="e49da-155"> 数据批注验证包</span><span class="sxs-lookup"><span data-stu-id="e49da-155"> data annotations validation package</span></span>

<span data-ttu-id="e49da-156">[AspNetCore.Blazor。DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)是使用 `DataAnnotationsValidator` 组件填充验证体验缺口的包。</span><span class="sxs-lookup"><span data-stu-id="e49da-156">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="e49da-157">包当前正在*试验*。</span><span class="sxs-lookup"><span data-stu-id="e49da-157">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="e49da-158">[CompareProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="e49da-158">[CompareProperty] attribute</span></span>

<span data-ttu-id="e49da-159"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 `DataAnnotationsValidator` 组件。</span><span class="sxs-lookup"><span data-stu-id="e49da-159">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="e49da-160">[AspNetCore.Blazor。DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *实验*包引入了一个附加的验证特性 `ComparePropertyAttribute`，该特性围绕这些限制。</span><span class="sxs-lookup"><span data-stu-id="e49da-160">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="e49da-161">在 Blazor 应用中，`[CompareProperty]` 是 `[Compare]` 属性的直接替换。</span><span class="sxs-lookup"><span data-stu-id="e49da-161">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span> <span data-ttu-id="e49da-162">有关详细信息，请参阅[CompareAttribute With OnValidSubmit EditForm （aspnet/AspNetCore #10643）已忽略](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748)。</span><span class="sxs-lookup"><span data-stu-id="e49da-162">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (aspnet/AspNetCore #10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="e49da-163">嵌套模型、集合类型和复杂类型</span><span class="sxs-lookup"><span data-stu-id="e49da-163">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="e49da-164"> 提供对通过内置 `DataAnnotationsValidator`使用数据批注验证窗体输入的支持。</span><span class="sxs-lookup"><span data-stu-id="e49da-164"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="e49da-165">但是，`DataAnnotationsValidator` 仅验证绑定到不是集合或复杂类型属性的窗体的模型的顶级属性。</span><span class="sxs-lookup"><span data-stu-id="e49da-165">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="e49da-166">若要验证绑定模型的整个对象关系图，包括集合和复杂类型属性，请使用BlazorAspNetCore 提供的 `ObjectGraphDataAnnotationsValidator` 。 [DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)包：</span><span class="sxs-lookup"><span data-stu-id="e49da-166">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="e49da-167">用 `[ValidateComplexType]`批注模型属性。</span><span class="sxs-lookup"><span data-stu-id="e49da-167">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="e49da-168">在下面的模型类中，`ShipDescription` 类包含在将模型绑定到窗体时要验证的其他数据批注：</span><span class="sxs-lookup"><span data-stu-id="e49da-168">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="e49da-169">*Starship.cs*：</span><span class="sxs-lookup"><span data-stu-id="e49da-169">*Starship.cs*:</span></span>

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

<span data-ttu-id="e49da-170">*ShipDescription.cs*：</span><span class="sxs-lookup"><span data-stu-id="e49da-170">*ShipDescription.cs*:</span></span>

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

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="e49da-171">验证复杂或集合类型属性</span><span class="sxs-lookup"><span data-stu-id="e49da-171">Validation of complex or collection type properties</span></span>

<span data-ttu-id="e49da-172">应用于模型属性的验证属性在提交表单时进行验证。</span><span class="sxs-lookup"><span data-stu-id="e49da-172">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="e49da-173">但是，在由 `DataAnnotationsValidator` 组件提交窗体时，不会验证模型的集合或复杂数据类型的属性。</span><span class="sxs-lookup"><span data-stu-id="e49da-173">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="e49da-174">若要在此方案中遵守嵌套验证属性，请使用自定义验证组件。</span><span class="sxs-lookup"><span data-stu-id="e49da-174">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="e49da-175">有关示例，请参阅[Blazor 验证示例（aspnet/示例）](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)。</span><span class="sxs-lookup"><span data-stu-id="e49da-175">For an example, see the [Blazor Validation sample (aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end
