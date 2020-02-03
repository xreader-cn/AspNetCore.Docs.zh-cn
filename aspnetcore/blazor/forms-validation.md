---
title: ASP.NET Core Blazor 窗体和验证
author: guardrex
description: 了解如何在 Blazor中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 2758bcbbc76c8a59716fe224dd2deb4ca8c06929
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726893"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a><span data-ttu-id="3f435-103">ASP.NET Core [!OP.NO-LOC(Blazor)] 窗体和验证</span><span class="sxs-lookup"><span data-stu-id="3f435-103">ASP.NET Core [!OP.NO-LOC(Blazor)] forms and validation</span></span>

<span data-ttu-id="3f435-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3f435-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3f435-105">使用[数据批注](xref:mvc/models/validation)[!OP.NO-LOC(Blazor)] 支持窗体和验证。</span><span class="sxs-lookup"><span data-stu-id="3f435-105">Forms and validation are supported in [!OP.NO-LOC(Blazor)] using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="3f435-106">以下 `ExampleModel` 类型使用数据批注定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="3f435-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="3f435-107">使用 `EditForm` 组件定义窗体。</span><span class="sxs-lookup"><span data-stu-id="3f435-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="3f435-108">下面的窗体演示典型的元素、组件和 Razor 代码：</span><span class="sxs-lookup"><span data-stu-id="3f435-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="3f435-109">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="3f435-109">In the preceding example:</span></span>

* <span data-ttu-id="3f435-110">窗体使用 `ExampleModel` 类型中定义的验证来验证 `name` 字段中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="3f435-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="3f435-111">该模型是在组件的 `@code` 块中创建的，并保存在私有字段（`_exampleModel`）中。</span><span class="sxs-lookup"><span data-stu-id="3f435-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="3f435-112">该字段将分配给 `<EditForm>` 元素的 `Model` 特性。</span><span class="sxs-lookup"><span data-stu-id="3f435-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="3f435-113">`InputText` 组件 `@bind-Value` 绑定：</span><span class="sxs-lookup"><span data-stu-id="3f435-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="3f435-114">模型属性（`_exampleModel.Name`）到 `InputText` 组件的 `Value` 属性。</span><span class="sxs-lookup"><span data-stu-id="3f435-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="3f435-115">`InputText` 组件的 `ValueChanged` 属性的 change 事件委托。</span><span class="sxs-lookup"><span data-stu-id="3f435-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="3f435-116">`DataAnnotationsValidator` 组件使用数据批注附加验证支持。</span><span class="sxs-lookup"><span data-stu-id="3f435-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="3f435-117">`ValidationSummary` 组件汇总验证消息。</span><span class="sxs-lookup"><span data-stu-id="3f435-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="3f435-118">当窗体成功提交（通过验证）时，将触发 `HandleValidSubmit`。</span><span class="sxs-lookup"><span data-stu-id="3f435-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="3f435-119">可使用一组内置输入组件来接收和验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="3f435-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="3f435-120">当输入发生更改和提交窗体时，将对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="3f435-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="3f435-121">下表显示了可用的输入组件。</span><span class="sxs-lookup"><span data-stu-id="3f435-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="3f435-122">输入组件</span><span class="sxs-lookup"><span data-stu-id="3f435-122">Input component</span></span> | <span data-ttu-id="3f435-123">呈现为&hellip;</span><span class="sxs-lookup"><span data-stu-id="3f435-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="3f435-124">所有输入组件（包括 `EditForm`）都支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="3f435-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="3f435-125">与组件参数不匹配的任何属性将添加到呈现的 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="3f435-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="3f435-126">输入组件提供默认行为，用于验证编辑和更改其 CSS 类以反映字段状态。</span><span class="sxs-lookup"><span data-stu-id="3f435-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="3f435-127">某些组件包含有用的分析逻辑。</span><span class="sxs-lookup"><span data-stu-id="3f435-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="3f435-128">例如，`InputDate` 和 `InputNumber` 通过将其注册为验证错误来适当地处理无法分析的值。</span><span class="sxs-lookup"><span data-stu-id="3f435-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="3f435-129">可以接受 null 值的类型还支持目标字段的为空性（例如 `int?`）。</span><span class="sxs-lookup"><span data-stu-id="3f435-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="3f435-130">以下 `Starship` 类型使用比之前 `ExampleModel`更多的属性和数据批注集定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="3f435-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="3f435-131">在前面的示例中，`Description` 是可选的，因为不存在任何数据批注。</span><span class="sxs-lookup"><span data-stu-id="3f435-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="3f435-132">以下窗体使用 `Starship` 模型中定义的验证来验证用户输入：</span><span class="sxs-lookup"><span data-stu-id="3f435-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="3f435-133">`EditForm` 创建一个 `EditContext` 作为[级联值](xref:blazor/components#cascading-values-and-parameters)，用于跟踪有关编辑过程的元数据，包括已修改的字段和当前验证消息。</span><span class="sxs-lookup"><span data-stu-id="3f435-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="3f435-134">`EditForm` 还为有效提交和无效提交（`OnValidSubmit`、`OnInvalidSubmit`）提供便利事件。</span><span class="sxs-lookup"><span data-stu-id="3f435-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="3f435-135">或者，使用 `OnSubmit` 使用自定义验证代码来触发验证和检查字段值。</span><span class="sxs-lookup"><span data-stu-id="3f435-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="3f435-136">在下例中：</span><span class="sxs-lookup"><span data-stu-id="3f435-136">In the following example:</span></span>

* <span data-ttu-id="3f435-137">选择 "**提交**" 按钮时，将运行 `HandleSubmit` 方法。</span><span class="sxs-lookup"><span data-stu-id="3f435-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="3f435-138">使用窗体的 `EditContext`验证窗体。</span><span class="sxs-lookup"><span data-stu-id="3f435-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="3f435-139">通过将 `EditContext` 传递给调用服务器上的 web API 终结点的 `ServerValidate` 方法来进一步验证窗体（*未显示*）。</span><span class="sxs-lookup"><span data-stu-id="3f435-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="3f435-140">根据客户端和服务器端验证的结果，运行其他代码，方法是检查 `isValid`。</span><span class="sxs-lookup"><span data-stu-id="3f435-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="3f435-141">基于输入事件的 InputText</span><span class="sxs-lookup"><span data-stu-id="3f435-141">InputText based on the input event</span></span>

<span data-ttu-id="3f435-142">使用 `InputText` 组件创建使用 `input` 事件而不是 `change` 事件的自定义组件。</span><span class="sxs-lookup"><span data-stu-id="3f435-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="3f435-143">使用以下标记创建组件，并使用组件，就像使用 `InputText` 一样：</span><span class="sxs-lookup"><span data-stu-id="3f435-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="3f435-144">使用单选按钮</span><span class="sxs-lookup"><span data-stu-id="3f435-144">Work with radio buttons</span></span>

<span data-ttu-id="3f435-145">使用窗体中的单选按钮时，数据绑定的处理方式与其他元素不同，因为单选按钮作为一个组进行计算。</span><span class="sxs-lookup"><span data-stu-id="3f435-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="3f435-146">每个单选按钮的值都是固定的，但单选按钮组的值是所选单选按钮的值。</span><span class="sxs-lookup"><span data-stu-id="3f435-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="3f435-147">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="3f435-147">The following example shows how to:</span></span>

* <span data-ttu-id="3f435-148">处理单选按钮组的数据绑定。</span><span class="sxs-lookup"><span data-stu-id="3f435-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="3f435-149">使用自定义 `InputRadio` 组件支持验证。</span><span class="sxs-lookup"><span data-stu-id="3f435-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="3f435-150">以下 `EditForm` 使用上述 `InputRadio` 组件获取和验证用户的评级：</span><span class="sxs-lookup"><span data-stu-id="3f435-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="3f435-151">验证支持</span><span class="sxs-lookup"><span data-stu-id="3f435-151">Validation support</span></span>

<span data-ttu-id="3f435-152">`DataAnnotationsValidator` 组件使用数据批注将验证支持附加到级联 `EditContext`。</span><span class="sxs-lookup"><span data-stu-id="3f435-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="3f435-153">使用数据批注启用验证支持需要此显式手势。</span><span class="sxs-lookup"><span data-stu-id="3f435-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="3f435-154">若要使用不同于数据批注的验证系统，请将 `DataAnnotationsValidator` 替换为自定义实现。</span><span class="sxs-lookup"><span data-stu-id="3f435-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="3f435-155">ASP.NET Core 实现可在引用源： [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)中进行检查。</span><span class="sxs-lookup"><span data-stu-id="3f435-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="3f435-156"> 执行两种类型的验证：</span><span class="sxs-lookup"><span data-stu-id="3f435-156"> performs two types of validation:</span></span>

* <span data-ttu-id="3f435-157">当用户在字段外切换时，将执行*字段验证*。</span><span class="sxs-lookup"><span data-stu-id="3f435-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="3f435-158">在字段验证过程中，`DataAnnotationsValidator` 组件将所有报告的验证结果与该字段相关联。</span><span class="sxs-lookup"><span data-stu-id="3f435-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="3f435-159">当用户提交窗体时，将执行*模型验证*。</span><span class="sxs-lookup"><span data-stu-id="3f435-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="3f435-160">在模型验证过程中，`DataAnnotationsValidator` 组件尝试基于验证结果报告的成员名称确定字段。</span><span class="sxs-lookup"><span data-stu-id="3f435-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="3f435-161">与单个成员无关的验证结果与模型关联，而不是与字段关联。</span><span class="sxs-lookup"><span data-stu-id="3f435-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="3f435-162">验证摘要和验证消息组件</span><span class="sxs-lookup"><span data-stu-id="3f435-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="3f435-163">`ValidationSummary` 组件汇总了与[验证摘要标记帮助](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)程序类似的所有验证消息：</span><span class="sxs-lookup"><span data-stu-id="3f435-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="3f435-164">使用 `Model` 参数输出特定模型的验证消息：</span><span class="sxs-lookup"><span data-stu-id="3f435-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="3f435-165">`ValidationMessage` 组件显示特定字段的验证消息，这类似于[验证消息标记帮助器](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="3f435-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="3f435-166">使用 `For` 特性指定验证字段，并使用命名模型属性的 lambda 表达式指定：</span><span class="sxs-lookup"><span data-stu-id="3f435-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="3f435-167">`ValidationMessage` 和 `ValidationSummary` 组件支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="3f435-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="3f435-168">与组件参数不匹配的任何属性将添加到生成的 `<div>` 或 `<ul>` 元素。</span><span class="sxs-lookup"><span data-stu-id="3f435-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="3f435-169">自定义验证特性</span><span class="sxs-lookup"><span data-stu-id="3f435-169">Custom validation attributes</span></span>

<span data-ttu-id="3f435-170">若要确保在使用[自定义验证属性](xref:mvc/models/validation#custom-attributes)时验证结果与字段正确关联，请在创建 <xref:System.ComponentModel.DataAnnotations.ValidationResult>时传递验证上下文的 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>：</span><span class="sxs-lookup"><span data-stu-id="3f435-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="3f435-171"> 数据批注验证包</span><span class="sxs-lookup"><span data-stu-id="3f435-171"> data annotations validation package</span></span>

<span data-ttu-id="3f435-172">[AspNetCore.Blazor。DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)是使用 `DataAnnotationsValidator` 组件填充验证体验缺口的包。</span><span class="sxs-lookup"><span data-stu-id="3f435-172">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="3f435-173">包当前正在*试验*。</span><span class="sxs-lookup"><span data-stu-id="3f435-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="3f435-174">[CompareProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="3f435-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="3f435-175"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 `DataAnnotationsValidator` 组件，因为它不会将验证结果与特定成员关联。</span><span class="sxs-lookup"><span data-stu-id="3f435-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="3f435-176">这可能会导致字段级验证之间的行为不一致，并在提交时验证整个模型。</span><span class="sxs-lookup"><span data-stu-id="3f435-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="3f435-177">[AspNetCore.Blazor。DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *实验*包引入了一个附加的验证特性 `ComparePropertyAttribute`，该特性围绕这些限制。</span><span class="sxs-lookup"><span data-stu-id="3f435-177">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="3f435-178">在 Blazor 应用中，`[CompareProperty]` 是 `[Compare]` 属性的直接替换。</span><span class="sxs-lookup"><span data-stu-id="3f435-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="3f435-179">嵌套模型、集合类型和复杂类型</span><span class="sxs-lookup"><span data-stu-id="3f435-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="3f435-180"> 提供对通过内置 `DataAnnotationsValidator`使用数据批注验证窗体输入的支持。</span><span class="sxs-lookup"><span data-stu-id="3f435-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="3f435-181">但是，`DataAnnotationsValidator` 仅验证绑定到不是集合或复杂类型属性的窗体的模型的顶级属性。</span><span class="sxs-lookup"><span data-stu-id="3f435-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="3f435-182">若要验证绑定模型的整个对象关系图，包括集合和复杂类型属性，请使用BlazorAspNetCore 提供的 `ObjectGraphDataAnnotationsValidator` 。 [DataAnnotations](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)包：</span><span class="sxs-lookup"><span data-stu-id="3f435-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="3f435-183">用 `[ValidateComplexType]`批注模型属性。</span><span class="sxs-lookup"><span data-stu-id="3f435-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="3f435-184">在下面的模型类中，`ShipDescription` 类包含在将模型绑定到窗体时要验证的其他数据批注：</span><span class="sxs-lookup"><span data-stu-id="3f435-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="3f435-185">*Starship.cs*：</span><span class="sxs-lookup"><span data-stu-id="3f435-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="3f435-186">*ShipDescription.cs*：</span><span class="sxs-lookup"><span data-stu-id="3f435-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="3f435-187">基于窗体验证启用 "提交" 按钮</span><span class="sxs-lookup"><span data-stu-id="3f435-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="3f435-188">若要启用和禁用基于窗体验证的 "提交" 按钮：</span><span class="sxs-lookup"><span data-stu-id="3f435-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="3f435-189">使用窗体的 `EditContext` 在初始化组件时分配模型。</span><span class="sxs-lookup"><span data-stu-id="3f435-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="3f435-190">验证上下文的 `OnFieldChanged` 回调中的窗体，以启用和禁用 "提交" 按钮。</span><span class="sxs-lookup"><span data-stu-id="3f435-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>

```razor
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

        _editContext.OnFieldChanged += (_, __) =>
        {
            _formInvalid = !_editContext.Validate();
            StateHasChanged();
        };
    }
}
```

<span data-ttu-id="3f435-191">在前面的示例中，将 `_formInvalid` 设置为 `false` 如果：</span><span class="sxs-lookup"><span data-stu-id="3f435-191">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="3f435-192">该窗体预加载了有效的默认值。</span><span class="sxs-lookup"><span data-stu-id="3f435-192">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="3f435-193">您希望在加载窗体时启用 "提交" 按钮。</span><span class="sxs-lookup"><span data-stu-id="3f435-193">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="3f435-194">上述方法的副作用是，在用户与任何一个字段交互后，使用无效的字段填充 `ValidationSummary` 组件。</span><span class="sxs-lookup"><span data-stu-id="3f435-194">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="3f435-195">可以通过以下方式之一解决此方案：</span><span class="sxs-lookup"><span data-stu-id="3f435-195">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="3f435-196">请勿使用窗体上的 `ValidationSummary` 组件。</span><span class="sxs-lookup"><span data-stu-id="3f435-196">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="3f435-197">选择 "提交" 按钮（例如，在 `HandleValidSubmit` 方法中）时，使 `ValidationSummary` 组件可见。</span><span class="sxs-lookup"><span data-stu-id="3f435-197">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
