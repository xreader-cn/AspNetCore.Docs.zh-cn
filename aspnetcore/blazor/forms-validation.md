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
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="5ac25-103">ASP.NET Core Blazor 窗体和验证</span><span class="sxs-lookup"><span data-stu-id="5ac25-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="5ac25-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5ac25-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5ac25-105">使用[数据批注](xref:mvc/models/validation)在 Blazor 中支持窗体和验证。</span><span class="sxs-lookup"><span data-stu-id="5ac25-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="5ac25-106">以下 `ExampleModel` 类型使用数据批注定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="5ac25-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="5ac25-107">使用 `EditForm` 组件定义窗体。</span><span class="sxs-lookup"><span data-stu-id="5ac25-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="5ac25-108">下面的窗体演示典型的元素、组件和 Razor 代码：</span><span class="sxs-lookup"><span data-stu-id="5ac25-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="5ac25-109">窗体使用 `ExampleModel` 类型中定义的验证来验证 `name` 字段中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="5ac25-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="5ac25-110">该模型是在组件的 `@code` 块中创建的，并保存在私有字段（`exampleModel`）中。</span><span class="sxs-lookup"><span data-stu-id="5ac25-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="5ac25-111">该字段将分配给 `<EditForm>` 元素的 `Model` 特性。</span><span class="sxs-lookup"><span data-stu-id="5ac25-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="5ac25-112">`DataAnnotationsValidator` 组件使用数据批注附加验证支持。</span><span class="sxs-lookup"><span data-stu-id="5ac25-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="5ac25-113">`ValidationSummary` 组件汇总验证消息。</span><span class="sxs-lookup"><span data-stu-id="5ac25-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="5ac25-114">当窗体成功提交（通过验证）时，将触发 `HandleValidSubmit`。</span><span class="sxs-lookup"><span data-stu-id="5ac25-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="5ac25-115">可使用一组内置输入组件来接收和验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="5ac25-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="5ac25-116">当输入发生更改和提交窗体时，将对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="5ac25-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="5ac25-117">下表显示了可用的输入组件。</span><span class="sxs-lookup"><span data-stu-id="5ac25-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="5ac25-118">输入组件</span><span class="sxs-lookup"><span data-stu-id="5ac25-118">Input component</span></span> | <span data-ttu-id="5ac25-119">呈现为&hellip;</span><span class="sxs-lookup"><span data-stu-id="5ac25-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="5ac25-120">所有输入组件（包括 `EditForm`）都支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="5ac25-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="5ac25-121">与组件参数不匹配的任何属性将添加到呈现的 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="5ac25-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="5ac25-122">输入组件提供默认行为，用于验证编辑和更改其 CSS 类以反映字段状态。</span><span class="sxs-lookup"><span data-stu-id="5ac25-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="5ac25-123">某些组件包含有用的分析逻辑。</span><span class="sxs-lookup"><span data-stu-id="5ac25-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="5ac25-124">例如，`InputDate` 和 `InputNumber` 通过将其注册为验证错误来适当地处理无法分析的值。</span><span class="sxs-lookup"><span data-stu-id="5ac25-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="5ac25-125">可以接受 null 值的类型还支持目标字段的为空性（例如 `int?`）。</span><span class="sxs-lookup"><span data-stu-id="5ac25-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="5ac25-126">以下 `Starship` 类型使用比之前 `ExampleModel`更多的属性和数据批注集定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="5ac25-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="5ac25-127">在前面的示例中，`Description` 是可选的，因为不存在任何数据批注。</span><span class="sxs-lookup"><span data-stu-id="5ac25-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="5ac25-128">以下窗体使用 `Starship` 模型中定义的验证来验证用户输入：</span><span class="sxs-lookup"><span data-stu-id="5ac25-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="5ac25-129">`EditForm` 创建一个 `EditContext` 作为[级联值](xref:blazor/components#cascading-values-and-parameters)，用于跟踪有关编辑过程的元数据，包括已修改的字段和当前验证消息。</span><span class="sxs-lookup"><span data-stu-id="5ac25-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="5ac25-130">`EditForm` 还为有效提交和无效提交（`OnValidSubmit`、`OnInvalidSubmit`）提供便利事件。</span><span class="sxs-lookup"><span data-stu-id="5ac25-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="5ac25-131">或者，使用 `OnSubmit` 使用自定义验证代码来触发验证和检查字段值。</span><span class="sxs-lookup"><span data-stu-id="5ac25-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="5ac25-132">基于输入事件的 InputText</span><span class="sxs-lookup"><span data-stu-id="5ac25-132">InputText based on the input event</span></span>

<span data-ttu-id="5ac25-133">使用 `InputText` 组件创建使用 `input` 事件而不是 `change` 事件的自定义组件。</span><span class="sxs-lookup"><span data-stu-id="5ac25-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="5ac25-134">使用以下标记创建组件，并使用组件，就像使用 `InputText` 一样：</span><span class="sxs-lookup"><span data-stu-id="5ac25-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="5ac25-135">验证支持</span><span class="sxs-lookup"><span data-stu-id="5ac25-135">Validation support</span></span>

<span data-ttu-id="5ac25-136">`DataAnnotationsValidator` 组件使用数据批注将验证支持附加到级联 `EditContext`。</span><span class="sxs-lookup"><span data-stu-id="5ac25-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="5ac25-137">使用数据批注启用验证支持需要此显式手势。</span><span class="sxs-lookup"><span data-stu-id="5ac25-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="5ac25-138">若要使用不同于数据批注的验证系统，请将 `DataAnnotationsValidator` 替换为自定义实现。</span><span class="sxs-lookup"><span data-stu-id="5ac25-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="5ac25-139">ASP.NET Core 实现可在引用源： [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)中进行检查。</span><span class="sxs-lookup"><span data-stu-id="5ac25-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

<span data-ttu-id="5ac25-140">`ValidationSummary` 组件汇总了所有验证消息，这类似于[验证摘要标记帮助器](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="5ac25-140">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="5ac25-141">`ValidationMessage` 组件显示特定字段的验证消息，这类似于[验证消息标记帮助器](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="5ac25-141">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="5ac25-142">使用 `For` 特性指定验证字段，并使用命名模型属性的 lambda 表达式指定：</span><span class="sxs-lookup"><span data-stu-id="5ac25-142">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="5ac25-143">`ValidationMessage` 和 `ValidationSummary` 组件支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="5ac25-143">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="5ac25-144">与组件参数不匹配的任何属性将添加到生成的 `<div>` 或 `<ul>` 元素。</span><span class="sxs-lookup"><span data-stu-id="5ac25-144">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="5ac25-145">**AspNetCore. Blazor. DataAnnotations 包**</span><span class="sxs-lookup"><span data-stu-id="5ac25-145">**Microsoft.AspNetCore.Blazor.DataAnnotations.Validation package**</span></span>

<span data-ttu-id="5ac25-146">[AspNetCore. Blazor. DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)是使用 `DataAnnotationsValidator` 组件填充验证体验缺口的包。</span><span class="sxs-lookup"><span data-stu-id="5ac25-146">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="5ac25-147">包当前是*实验*性的，我们计划在未来版本中将这些方案添加到 ASP.NET Core 框架中。</span><span class="sxs-lookup"><span data-stu-id="5ac25-147">The package is currently *experimental*, and we plan to add these scenarios into the ASP.NET Core framework in a future release.</span></span>

<span data-ttu-id="5ac25-148">`DataAnnotationsValidator` 组件不会验证验证模型上的复杂属性的子属性。</span><span class="sxs-lookup"><span data-stu-id="5ac25-148">The `DataAnnotationsValidator` component doesn't validate subproperties of complex properties on a validating model.</span></span> <span data-ttu-id="5ac25-149">集合类型的项不会进行验证。</span><span class="sxs-lookup"><span data-stu-id="5ac25-149">Items of collection-type properties aren't validated.</span></span> <span data-ttu-id="5ac25-150">为了验证这些类型，`Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` 包引入了 `ValidateComplexType` 验证特性，该特性与 `ObjectGraphDataAnnotationsValidator` 组件一起使用。</span><span class="sxs-lookup"><span data-stu-id="5ac25-150">To validate these types, the `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` package introduces the `ValidateComplexType` validation attribute that works in tandem with the `ObjectGraphDataAnnotationsValidator` component.</span></span> <span data-ttu-id="5ac25-151">有关所使用的这些类型的示例，请参阅[aspnet/Samples GitHub 存储库中的 Blazor 验证示例](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)。</span><span class="sxs-lookup"><span data-stu-id="5ac25-151">For an example of these types in use, see the [Blazor Validation sample in the aspnet/samples GitHub repository ](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

<span data-ttu-id="5ac25-152"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 `DataAnnotationsValidator` 组件。</span><span class="sxs-lookup"><span data-stu-id="5ac25-152">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="5ac25-153">`Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` 包引入了一个附加的验证特性 `ComparePropertyAttribute`，用于解决这些限制。</span><span class="sxs-lookup"><span data-stu-id="5ac25-153">The `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="5ac25-154">在 Blazor 应用中，`ComparePropertyAttribute` 是 `CompareAttribute`的直接替换。</span><span class="sxs-lookup"><span data-stu-id="5ac25-154">In a Blazor app, `ComparePropertyAttribute` is a direct replacement for the `CompareAttribute`.</span></span> <span data-ttu-id="5ac25-155">有关详细信息，请参阅[CompareAttribute With OnValidSubmit EditForm （aspnet/AspNetCore \#10643）已忽略](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748)。</span><span class="sxs-lookup"><span data-stu-id="5ac25-155">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (aspnet/AspNetCore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="5ac25-156">验证复杂或集合类型属性</span><span class="sxs-lookup"><span data-stu-id="5ac25-156">Validation of complex or collection type properties</span></span>

<span data-ttu-id="5ac25-157">应用于模型属性的验证属性在提交表单时进行验证。</span><span class="sxs-lookup"><span data-stu-id="5ac25-157">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="5ac25-158">但是，在由 `DataAnnotationsValidator` 组件提交窗体时，不会验证模型的集合或复杂数据类型的属性。</span><span class="sxs-lookup"><span data-stu-id="5ac25-158">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="5ac25-159">若要在此方案中遵守嵌套验证属性，请使用自定义验证组件。</span><span class="sxs-lookup"><span data-stu-id="5ac25-159">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="5ac25-160">有关示例，请参阅[aspnet/Samples GitHub 存储库中的 Blazor 验证示例](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)。</span><span class="sxs-lookup"><span data-stu-id="5ac25-160">For an example, see the [Blazor Validation sample in the aspnet/samples GitHub repository](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end
