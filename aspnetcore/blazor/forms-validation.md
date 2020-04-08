---
title: ASP.NET Core Blazor 窗体和验证
author: guardrex
description: 了解如何在 Blazor 中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218955"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="7eff4-103">ASP.NET Core Blazor 窗体和验证</span><span class="sxs-lookup"><span data-stu-id="7eff4-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="7eff4-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7eff4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7eff4-105">Blazor 使用[数据注释](xref:mvc/models/validation)支持窗体和验证。</span><span class="sxs-lookup"><span data-stu-id="7eff4-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="7eff4-106">下面的 `ExampleModel` 类型使用数据注释定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="7eff4-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="7eff4-107">窗体是使用 `EditForm` 组件定义的。</span><span class="sxs-lookup"><span data-stu-id="7eff4-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="7eff4-108">以下窗体演示了典型的元素、组件和 Razor 代码：</span><span class="sxs-lookup"><span data-stu-id="7eff4-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="7eff4-109">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="7eff4-109">In the preceding example:</span></span>

* <span data-ttu-id="7eff4-110">该窗体使用 `ExampleModel` 类型中定义的验证来验证 `name` 字段中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="7eff4-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="7eff4-111">该模型在组件的 `@code` 块中创建，并保存在私有字段 (`_exampleModel`) 中。</span><span class="sxs-lookup"><span data-stu-id="7eff4-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="7eff4-112">该字段分配给 `<EditForm>` 元素的 `Model` 属性。</span><span class="sxs-lookup"><span data-stu-id="7eff4-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="7eff4-113">`InputText` 组件的 `@bind-Value` 进行以下绑定：</span><span class="sxs-lookup"><span data-stu-id="7eff4-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="7eff4-114">将模型属性 (`_exampleModel.Name`) 绑定到 `InputText` 组件的 `Value` 属性。</span><span class="sxs-lookup"><span data-stu-id="7eff4-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="7eff4-115">将更改事件委托绑定到 `InputText` 组件的 `ValueChanged` 属性。</span><span class="sxs-lookup"><span data-stu-id="7eff4-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="7eff4-116">`DataAnnotationsValidator` 组件使用数据注释附加验证支持。</span><span class="sxs-lookup"><span data-stu-id="7eff4-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="7eff4-117">`ValidationSummary` 组件汇总验证消息。</span><span class="sxs-lookup"><span data-stu-id="7eff4-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="7eff4-118">窗体成功提交（通过验证）时触发 `HandleValidSubmit`。</span><span class="sxs-lookup"><span data-stu-id="7eff4-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="7eff4-119">可使用一组内置的输入组件来接收和验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="7eff4-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="7eff4-120">当更改输入和提交窗体时，将验证输入。</span><span class="sxs-lookup"><span data-stu-id="7eff4-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="7eff4-121">下表显示了可用的输入组件。</span><span class="sxs-lookup"><span data-stu-id="7eff4-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="7eff4-122">输入组件</span><span class="sxs-lookup"><span data-stu-id="7eff4-122">Input component</span></span> | <span data-ttu-id="7eff4-123">呈现为&hellip;</span><span class="sxs-lookup"><span data-stu-id="7eff4-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="7eff4-124">所有输入组件（包括 `EditForm`）都支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="7eff4-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="7eff4-125">与某个组件参数不匹配的所有属性都将添加到呈现的 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="7eff4-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="7eff4-126">输入组件为编辑时验证以及更改其 CSS 类以反映字段状态提供默认行为。</span><span class="sxs-lookup"><span data-stu-id="7eff4-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="7eff4-127">某些组件包含有用的分析逻辑。</span><span class="sxs-lookup"><span data-stu-id="7eff4-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="7eff4-128">例如，`InputDate` 和 `InputNumber` 通过将无法分析的值注册为验证错误，以恰当的方式来处理它们。</span><span class="sxs-lookup"><span data-stu-id="7eff4-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="7eff4-129">可接受 Null 值的类型也支持目标字段的为 Null 性（例如，`int?`）。</span><span class="sxs-lookup"><span data-stu-id="7eff4-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="7eff4-130">下面的 `Starship` 类型使用比之前的 `ExampleModel` 更大的属性和数据注释集来定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="7eff4-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="7eff4-131">在上面的示例中，`Description` 是可选的，因为不存在任何数据注释。</span><span class="sxs-lookup"><span data-stu-id="7eff4-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="7eff4-132">以下窗体使用 `Starship` 模型中定义的验证来验证用户输入：</span><span class="sxs-lookup"><span data-stu-id="7eff4-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="7eff4-133">`EditForm` 创建一个 `EditContext` 作为[级联值](xref:blazor/components#cascading-values-and-parameters)来跟踪有关编辑过程的元数据，其中包括已修改的字段和当前的验证消息。</span><span class="sxs-lookup"><span data-stu-id="7eff4-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="7eff4-134">`EditForm` 还为有效和无效的提交提供便捷的事件（`OnValidSubmit`、`OnInvalidSubmit`）。</span><span class="sxs-lookup"><span data-stu-id="7eff4-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="7eff4-135">或者，使用 `OnSubmit` 触发验证并使用自定义验证代码检查字段值。</span><span class="sxs-lookup"><span data-stu-id="7eff4-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="7eff4-136">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="7eff4-136">In the following example:</span></span>

* <span data-ttu-id="7eff4-137">选择“提交”按钮时，将运行 `HandleSubmit` 方法  。</span><span class="sxs-lookup"><span data-stu-id="7eff4-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="7eff4-138">使用窗体的 `EditContext` 验证窗体。</span><span class="sxs-lookup"><span data-stu-id="7eff4-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="7eff4-139">通过将 `EditContext` 传递给 `ServerValidate` 方法来进一步验证窗体，该方法会调用服务器上的 Web API 终结点（*未显示*）。</span><span class="sxs-lookup"><span data-stu-id="7eff4-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="7eff4-140">通过检查 `isValid` 获得客户端和服务器端验证的结果，并根据该结果运行其他代码。</span><span class="sxs-lookup"><span data-stu-id="7eff4-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="7eff4-141">基于输入事件的 InputText</span><span class="sxs-lookup"><span data-stu-id="7eff4-141">InputText based on the input event</span></span>

<span data-ttu-id="7eff4-142">使用 `InputText` 组件创建一个使用 `input` 事件而不是 `change` 事件的自定义组件。</span><span class="sxs-lookup"><span data-stu-id="7eff4-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="7eff4-143">使用以下标记创建一个组件，并像使用 `InputText` 一样使用该组件：</span><span class="sxs-lookup"><span data-stu-id="7eff4-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="7eff4-144">使用单选按钮</span><span class="sxs-lookup"><span data-stu-id="7eff4-144">Work with radio buttons</span></span>

<span data-ttu-id="7eff4-145">使用窗体中的单选按钮时，数据绑定的处理方式与其他元素不同，因为单选按钮是作为一个组进行计算的。</span><span class="sxs-lookup"><span data-stu-id="7eff4-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="7eff4-146">每个单选按钮的值是固定的，但单选按钮组的值是所选单选按钮的值。</span><span class="sxs-lookup"><span data-stu-id="7eff4-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="7eff4-147">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="7eff4-147">The following example shows how to:</span></span>

* <span data-ttu-id="7eff4-148">处理单选按钮组的数据绑定。</span><span class="sxs-lookup"><span data-stu-id="7eff4-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="7eff4-149">使用自定义 `InputRadio` 组件支持验证。</span><span class="sxs-lookup"><span data-stu-id="7eff4-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="7eff4-150">以下 `EditForm` 使用前面的 `InputRadio` 组件来获取和验证用户的评级：</span><span class="sxs-lookup"><span data-stu-id="7eff4-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="7eff4-151">验证支持</span><span class="sxs-lookup"><span data-stu-id="7eff4-151">Validation support</span></span>

<span data-ttu-id="7eff4-152">`DataAnnotationsValidator` 组件使用数据注释将验证支持附加到级联的 `EditContext`。</span><span class="sxs-lookup"><span data-stu-id="7eff4-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="7eff4-153">使用数据注释启用对验证的支持需要此显式手势。</span><span class="sxs-lookup"><span data-stu-id="7eff4-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="7eff4-154">若要使用不同于数据注释的验证系统，请用自定义实现替换 `DataAnnotationsValidator`。</span><span class="sxs-lookup"><span data-stu-id="7eff4-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="7eff4-155">可在以下参考源中检查 ASP.NET Core 的实现：[DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="7eff4-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="7eff4-156"> 执行两种类型的验证：</span><span class="sxs-lookup"><span data-stu-id="7eff4-156"> performs two types of validation:</span></span>

* <span data-ttu-id="7eff4-157">当用户从某个字段中跳出时，将执行*字段验证*。</span><span class="sxs-lookup"><span data-stu-id="7eff4-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="7eff4-158">在字段验证期间，`DataAnnotationsValidator` 组件将报告的所有验证结果与该字段相关联。</span><span class="sxs-lookup"><span data-stu-id="7eff4-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="7eff4-159">当用户提交窗体时，将执行*模型验证*。</span><span class="sxs-lookup"><span data-stu-id="7eff4-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="7eff4-160">在模型验证期间，`DataAnnotationsValidator` 组件尝试根据验证结果报告的成员名称来确定字段。</span><span class="sxs-lookup"><span data-stu-id="7eff4-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="7eff4-161">与单个成员无关的验证结果将与模型而不是字段相关联。</span><span class="sxs-lookup"><span data-stu-id="7eff4-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="7eff4-162">验证摘要和验证消息组件</span><span class="sxs-lookup"><span data-stu-id="7eff4-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="7eff4-163">`ValidationSummary` 组件用于汇总所有验证消息，这与[验证摘要标记帮助程序](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)类似：</span><span class="sxs-lookup"><span data-stu-id="7eff4-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="7eff4-164">使用 `Model` 参数输出特定模型的验证消息：</span><span class="sxs-lookup"><span data-stu-id="7eff4-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="7eff4-165">`ValidationMessage` 组件用于显示特定字段的验证消息，这与[验证消息标记帮助程序](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)类似。</span><span class="sxs-lookup"><span data-stu-id="7eff4-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="7eff4-166">使用 `For` 属性和一个为模型属性命名的 Lambda 表达式来指定要验证的字段：</span><span class="sxs-lookup"><span data-stu-id="7eff4-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="7eff4-167">`ValidationMessage` 和 `ValidationSummary` 组件支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="7eff4-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="7eff4-168">与某个组件参数不匹配的所有属性都将添加到生成的 `<div>` 或 `<ul>` 元素中。</span><span class="sxs-lookup"><span data-stu-id="7eff4-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="7eff4-169">自定义验证属性</span><span class="sxs-lookup"><span data-stu-id="7eff4-169">Custom validation attributes</span></span>

<span data-ttu-id="7eff4-170">当使用[自定义验证属性](xref:mvc/models/validation#custom-attributes)时，为确保验证结果与字段正确关联，请在创建 <xref:System.ComponentModel.DataAnnotations.ValidationResult> 时传递验证上下文的 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>：</span><span class="sxs-lookup"><span data-stu-id="7eff4-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="7eff4-171"> 数据注释验证包</span><span class="sxs-lookup"><span data-stu-id="7eff4-171"> data annotations validation package</span></span>

<span data-ttu-id="7eff4-172">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 是一个使用 `DataAnnotationsValidator` 组件填补验证体验缺口的验证包。</span><span class="sxs-lookup"><span data-stu-id="7eff4-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="7eff4-173">该包目前处于*试验阶段*。</span><span class="sxs-lookup"><span data-stu-id="7eff4-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="7eff4-174">[CompareProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="7eff4-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="7eff4-175"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 `DataAnnotationsValidator` 组件，因为它不会将验证结果与特定成员关联。</span><span class="sxs-lookup"><span data-stu-id="7eff4-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="7eff4-176">这可能会导致字段级验证的行为与提交时整个模型的验证行为不一致。</span><span class="sxs-lookup"><span data-stu-id="7eff4-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="7eff4-177">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 试验性包引入了一个附加的验证属性 `ComparePropertyAttribute`，它可以克服这些限制  。</span><span class="sxs-lookup"><span data-stu-id="7eff4-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="7eff4-178">在 Blazor 应用中，`[CompareProperty]` 可直接替代 `[Compare]` 属性。</span><span class="sxs-lookup"><span data-stu-id="7eff4-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="7eff4-179">嵌套模型、集合类型和复杂类型</span><span class="sxs-lookup"><span data-stu-id="7eff4-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="7eff4-180"> 支持结合使用数据注释和内置的 `DataAnnotationsValidator` 来验证窗体输入。</span><span class="sxs-lookup"><span data-stu-id="7eff4-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="7eff4-181">但是，`DataAnnotationsValidator` 仅验证绑定到窗体的模型的顶级属性（不包括集合类型或复杂类型的属性）。</span><span class="sxs-lookup"><span data-stu-id="7eff4-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="7eff4-182">若要验证绑定模型的整个对象图（包括集合类型和复杂类型的属性），请使用试验性 [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 包提供的 `ObjectGraphDataAnnotationsValidator`  ：</span><span class="sxs-lookup"><span data-stu-id="7eff4-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="7eff4-183">用 `[ValidateComplexType]` 注释模型属性。</span><span class="sxs-lookup"><span data-stu-id="7eff4-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="7eff4-184">在以下模型类中，`ShipDescription` 类包含附加数据注释，用于在将模型绑定到窗体时进行验证：</span><span class="sxs-lookup"><span data-stu-id="7eff4-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="7eff4-185">*Starship.cs*：</span><span class="sxs-lookup"><span data-stu-id="7eff4-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="7eff4-186">*ShipDescription.cs*：</span><span class="sxs-lookup"><span data-stu-id="7eff4-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="7eff4-187">基于窗体验证启用提交按钮</span><span class="sxs-lookup"><span data-stu-id="7eff4-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="7eff4-188">若要基于窗体验证启用和禁用提交按钮，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7eff4-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="7eff4-189">使用窗体的 `EditContext` 在初始化组件时分配模型。</span><span class="sxs-lookup"><span data-stu-id="7eff4-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="7eff4-190">在上下文的 `OnFieldChanged` 回调中验证窗体，以启用和禁用提交按钮。</span><span class="sxs-lookup"><span data-stu-id="7eff4-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="7eff4-191">解除挂接 `Dispose` 方法中的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="7eff4-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="7eff4-192">有关详细信息，请参阅 <xref:blazor/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="7eff4-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

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
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="7eff4-193">在上面的示例中，如果满足以下条件，则将 `_formInvalid` 设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="7eff4-193">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="7eff4-194">窗体已预加载有效的默认值。</span><span class="sxs-lookup"><span data-stu-id="7eff4-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="7eff4-195">你希望在加载窗体时启用提交按钮。</span><span class="sxs-lookup"><span data-stu-id="7eff4-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="7eff4-196">上述方法的副作用是在用户与任何一个字段进行交互后，`ValidationSummary` 组件都会填充无效的字段。</span><span class="sxs-lookup"><span data-stu-id="7eff4-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="7eff4-197">可通过以下方式之一解决此情况：</span><span class="sxs-lookup"><span data-stu-id="7eff4-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="7eff4-198">不在窗体上使用 `ValidationSummary` 组件。</span><span class="sxs-lookup"><span data-stu-id="7eff4-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="7eff4-199">选择提交按钮时，使 `ValidationSummary` 组件可见（例如，在 `HandleValidSubmit` 方法中）。</span><span class="sxs-lookup"><span data-stu-id="7eff4-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
