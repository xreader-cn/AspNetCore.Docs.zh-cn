---
title: ASP.NET Core Blazor 窗体和验证
author: guardrex
description: 了解如何在 Blazor 中使用窗体和字段验证方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 1287ab5ce61e58848329c96393c3ee8c37610245
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658685"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="c03d5-103">ASP.NET Core Blazor 窗体和验证</span><span class="sxs-lookup"><span data-stu-id="c03d5-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="c03d5-104">作者：[Daniel Roth](https://github.com/danroth27)、[Rémi Bourgarel](https://remibou.github.io/) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c03d5-104">By [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c03d5-105">在 Blazor 中，使用[数据注释](xref:mvc/models/validation)支持窗体和验证。</span><span class="sxs-lookup"><span data-stu-id="c03d5-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="c03d5-106">下面的 `ExampleModel` 类型使用数据注释定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="c03d5-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="c03d5-107">窗体是使用 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 组件定义的。</span><span class="sxs-lookup"><span data-stu-id="c03d5-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="c03d5-108">以下窗体展示了典型的元素、组件和 Razor 代码：</span><span class="sxs-lookup"><span data-stu-id="c03d5-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
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
        ...
    }
}
```

<span data-ttu-id="c03d5-109">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="c03d5-109">In the preceding example:</span></span>

* <span data-ttu-id="c03d5-110">该窗体使用 `ExampleModel` 类型中定义的验证来验证 `name` 字段中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="c03d5-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="c03d5-111">该模型在组件的 `@code` 块中创建，并保存在私有字段 (`exampleModel`) 中。</span><span class="sxs-lookup"><span data-stu-id="c03d5-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="c03d5-112">该字段分配给 `<EditForm>` 元素的 `Model` 属性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="c03d5-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件的 `@bind-Value` 进行以下绑定：</span><span class="sxs-lookup"><span data-stu-id="c03d5-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="c03d5-114">将模型属性 (`exampleModel.Name`) 绑定到 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件的 `Value` 属性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="c03d5-115">有关属性绑定的详细信息，请参阅 <xref:blazor/components/data-binding#binding-with-component-parameters>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-115">For more information on property binding, see <xref:blazor/components/data-binding#binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="c03d5-116">将更改事件委托绑定到 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件的 `ValueChanged` 属性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="c03d5-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [验证器组件](#validator-components)使用数据注释附加验证支持。</span><span class="sxs-lookup"><span data-stu-id="c03d5-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="c03d5-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件汇总验证消息。</span><span class="sxs-lookup"><span data-stu-id="c03d5-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="c03d5-119">窗体成功提交（通过验证）时触发 `HandleValidSubmit`。</span><span class="sxs-lookup"><span data-stu-id="c03d5-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="c03d5-120">内置窗体组件</span><span class="sxs-lookup"><span data-stu-id="c03d5-120">Built-in forms components</span></span>

<span data-ttu-id="c03d5-121">可使用一组内置的组件来接收和验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="c03d5-121">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="c03d5-122">当更改输入和提交窗体时，将验证输入。</span><span class="sxs-lookup"><span data-stu-id="c03d5-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="c03d5-123">下表显示了可用的输入组件。</span><span class="sxs-lookup"><span data-stu-id="c03d5-123">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="c03d5-124">输入组件</span><span class="sxs-lookup"><span data-stu-id="c03d5-124">Input component</span></span> | <span data-ttu-id="c03d5-125">呈现为&hellip;</span><span class="sxs-lookup"><span data-stu-id="c03d5-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="c03d5-126">输入组件</span><span class="sxs-lookup"><span data-stu-id="c03d5-126">Input component</span></span> | <span data-ttu-id="c03d5-127">呈现为&hellip;</span><span class="sxs-lookup"><span data-stu-id="c03d5-127">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> <span data-ttu-id="c03d5-128">`InputRadio` 和 `InputRadioGroup` 组件在 ASP.NET Core 5.0 或更高版本中可用。</span><span class="sxs-lookup"><span data-stu-id="c03d5-128">The `InputRadio` and `InputRadioGroup` components are available in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="c03d5-129">有关详细信息，请选择本文的 5.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c03d5-129">For more information, select a 5.0 or later version of this article.</span></span>

::: moniker-end

<span data-ttu-id="c03d5-130">所有输入组件（包括 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>）都支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-130">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="c03d5-131">与某个组件参数不匹配的所有属性都将添加到呈现的 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="c03d5-131">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="c03d5-132">输入组件为验证字段何时更改（包括更新字段 CSS 类以反映字段状态）提供默认行为。</span><span class="sxs-lookup"><span data-stu-id="c03d5-132">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="c03d5-133">某些组件包含有用的分析逻辑。</span><span class="sxs-lookup"><span data-stu-id="c03d5-133">Some components include useful parsing logic.</span></span> <span data-ttu-id="c03d5-134">例如，<xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 和 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 通过将无法分析的值注册为验证错误，以恰当的方式来处理无法分析的值。</span><span class="sxs-lookup"><span data-stu-id="c03d5-134">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="c03d5-135">可接受 Null 值的类型也支持目标字段的为 Null 性（例如，`int?`）。</span><span class="sxs-lookup"><span data-stu-id="c03d5-135">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="c03d5-136">下面的 `Starship` 类型使用比之前的 `ExampleModel` 更大的属性和数据注释集来定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="c03d5-136">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="c03d5-137">在上面的示例中，`Description` 是可选的，因为不存在任何数据注释。</span><span class="sxs-lookup"><span data-stu-id="c03d5-137">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="c03d5-138">以下窗体使用 `Starship` 模型中定义的验证来验证用户输入：</span><span class="sxs-lookup"><span data-stu-id="c03d5-138">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
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
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="c03d5-139"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> 创建一个 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 作为[级联值](xref:blazor/components/cascading-values-and-parameters)来跟踪有关编辑过程的元数据，其中包括已修改的字段和当前的验证消息。</span><span class="sxs-lookup"><span data-stu-id="c03d5-139">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="c03d5-140">将 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 或 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> 分配给 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 。</span><span class="sxs-lookup"><span data-stu-id="c03d5-140">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="c03d5-141">不支持同时分配两者，会生成运行时错误。</span><span class="sxs-lookup"><span data-stu-id="c03d5-141">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="c03d5-142"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> 为有效和无效的窗体提交提供便捷的事件：</span><span class="sxs-lookup"><span data-stu-id="c03d5-142">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="c03d5-143">通过 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>，使用自定义代码触发验证并检查字段值。</span><span class="sxs-lookup"><span data-stu-id="c03d5-143">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="c03d5-144">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="c03d5-144">In the following example:</span></span>

* <span data-ttu-id="c03d5-145">选择 `Submit` 按钮时，执行 `HandleSubmit` 方法。</span><span class="sxs-lookup"><span data-stu-id="c03d5-145">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="c03d5-146">通过调用 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> 验证窗体。</span><span class="sxs-lookup"><span data-stu-id="c03d5-146">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="c03d5-147">根据验证结果执行其他代码。</span><span class="sxs-lookup"><span data-stu-id="c03d5-147">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="c03d5-148">将业务逻辑放在分配给 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> 的方法中。</span><span class="sxs-lookup"><span data-stu-id="c03d5-148">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="c03d5-149">Framework API 不存在，无法直接从 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 清除验证消息。</span><span class="sxs-lookup"><span data-stu-id="c03d5-149">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="c03d5-150">因此，通常建议不要在窗体中将验证消息添加到新的 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-150">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="c03d5-151">若要管理验证消息，请将[验证器组件](#validator-components)与[业务逻辑验证代码](#business-logic-validation)一起使用，如本文所述。</span><span class="sxs-lookup"><span data-stu-id="c03d5-151">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="c03d5-152">显示名称支持</span><span class="sxs-lookup"><span data-stu-id="c03d5-152">Display name support</span></span>

<span data-ttu-id="c03d5-153">*本部分应用于 .NET 5 候选发布 1 (RC1) 或更高版本中的 ASP.NET Core。*</span><span class="sxs-lookup"><span data-stu-id="c03d5-153">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="c03d5-154">以下内置组件支持带有 `DisplayName` 参数的显示名称：</span><span class="sxs-lookup"><span data-stu-id="c03d5-154">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="c03d5-155">在下面的 `InputDate` 组件示例中：</span><span class="sxs-lookup"><span data-stu-id="c03d5-155">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="c03d5-156">显示名称 (`DisplayName`) 设置为 `birthday`。</span><span class="sxs-lookup"><span data-stu-id="c03d5-156">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="c03d5-157">该组件作为 `DateTime` 类型绑定到 `BirthDate` 属性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-157">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="c03d5-158">如果用户不提供日期值，则验证错误将显示为：</span><span class="sxs-lookup"><span data-stu-id="c03d5-158">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="c03d5-159">验证器组件</span><span class="sxs-lookup"><span data-stu-id="c03d5-159">Validator components</span></span>

<span data-ttu-id="c03d5-160">验证器组件通过管理窗体的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 的 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> 来支持窗体验证。</span><span class="sxs-lookup"><span data-stu-id="c03d5-160">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="c03d5-161">Blazor 框架提供了 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件，以将验证支持附加到基于[验证属性（数据批注）](xref:mvc/models/validation#validation-attributes)的窗体。</span><span class="sxs-lookup"><span data-stu-id="c03d5-161">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="c03d5-162">创建自定义验证器组件，以处理同一页上不同窗体或不同窗体处理步骤上相同窗体的验证消息，例如客户端验证，然后是服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="c03d5-162">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="c03d5-163">本文的以下部分将使用本部分 `CustomValidator` 中所示的验证器组件示例：</span><span class="sxs-lookup"><span data-stu-id="c03d5-163">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="c03d5-164">业务逻辑验证</span><span class="sxs-lookup"><span data-stu-id="c03d5-164">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="c03d5-165">服务器验证</span><span class="sxs-lookup"><span data-stu-id="c03d5-165">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="c03d5-166">在许多情况下，可使用自定义数据注释验证属性来代替自定义验证器组件。</span><span class="sxs-lookup"><span data-stu-id="c03d5-166">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="c03d5-167">应用于窗体模型的自定义属性使用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件激活。</span><span class="sxs-lookup"><span data-stu-id="c03d5-167">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="c03d5-168">当与服务器端验证一起使用时，应用于模型的所有自定义属性都必须可在服务器上执行。</span><span class="sxs-lookup"><span data-stu-id="c03d5-168">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="c03d5-169">有关详细信息，请参阅 <xref:mvc/models/validation#alternatives-to-built-in-attributes>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-169">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="c03d5-170">从 <xref:Microsoft.AspNetCore.Components.ComponentBase> 创建验证器组件：</span><span class="sxs-lookup"><span data-stu-id="c03d5-170">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="c03d5-171">窗体的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 是组件的[级联参数](xref:blazor/components/cascading-values-and-parameters)。</span><span class="sxs-lookup"><span data-stu-id="c03d5-171">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="c03d5-172">初始化验证器组件时，将创建一个新的 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> 来维护当前的窗体错误列表。</span><span class="sxs-lookup"><span data-stu-id="c03d5-172">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="c03d5-173">当窗体组件中的开发人员代码调用 `DisplayErrors` 方法时，消息存储接收错误。</span><span class="sxs-lookup"><span data-stu-id="c03d5-173">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="c03d5-174">这些错误会传递到 [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) 中的 `DisplayErrors` 方法。</span><span class="sxs-lookup"><span data-stu-id="c03d5-174">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="c03d5-175">在字典中，键是具有一个或多个错误的窗体字段的名称。</span><span class="sxs-lookup"><span data-stu-id="c03d5-175">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="c03d5-176">值为错误列表。</span><span class="sxs-lookup"><span data-stu-id="c03d5-176">The value is the error list.</span></span>
* <span data-ttu-id="c03d5-177">发生以下任一情况时，将清除消息：</span><span class="sxs-lookup"><span data-stu-id="c03d5-177">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="c03d5-178">引发 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 事件时，会在 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> 上请求验证。</span><span class="sxs-lookup"><span data-stu-id="c03d5-178">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="c03d5-179">所有错误都将被清除。</span><span class="sxs-lookup"><span data-stu-id="c03d5-179">All of the errors are cleared.</span></span>
  * <span data-ttu-id="c03d5-180">引发 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 事件时，窗体中的字段会更改。</span><span class="sxs-lookup"><span data-stu-id="c03d5-180">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="c03d5-181">仅清除字段的错误。</span><span class="sxs-lookup"><span data-stu-id="c03d5-181">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="c03d5-182">`ClearErrors` 方法由开发人员代码调用。</span><span class="sxs-lookup"><span data-stu-id="c03d5-182">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="c03d5-183">所有错误都将被清除。</span><span class="sxs-lookup"><span data-stu-id="c03d5-183">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

public class CustomValidator : ComponentBase
{
    private ValidationMessageStore messageStore;

    [CascadingParameter]
    private EditContext CurrentEditContext { get; set; }

    protected override void OnInitialized()
    {
        if (CurrentEditContext == null)
        {
            throw new InvalidOperationException(
                $"{nameof(CustomValidator)} requires a cascading " +
                $"parameter of type {nameof(EditContext)}. " +
                $"For example, you can use {nameof(CustomValidator)} " +
                $"inside an {nameof(EditForm)}.");
        }

        messageStore = new ValidationMessageStore(CurrentEditContext);

        CurrentEditContext.OnValidationRequested += (s, e) => 
            messageStore.Clear();
        CurrentEditContext.OnFieldChanged += (s, e) => 
            messageStore.Clear(e.FieldIdentifier);
    }

    public void DisplayErrors(Dictionary<string, List<string>> errors)
    {
        foreach (var err in errors)
        {
            messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
        }

        CurrentEditContext.NotifyValidationStateChanged();
    }

    public void ClearErrors()
    {
        messageStore.Clear();
        CurrentEditContext.NotifyValidationStateChanged();
    }
}
```

## <a name="business-logic-validation"></a><span data-ttu-id="c03d5-184">业务逻辑验证</span><span class="sxs-lookup"><span data-stu-id="c03d5-184">Business logic validation</span></span>

<span data-ttu-id="c03d5-185">可通过接收字典中的窗体错误的[验证器组件](#validator-components)完成业务逻辑验证。</span><span class="sxs-lookup"><span data-stu-id="c03d5-185">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="c03d5-186">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="c03d5-186">In the following example:</span></span>

* <span data-ttu-id="c03d5-187">使用本文的[验证器组件](#validator-components)部分的 `CustomValidator` 组件。</span><span class="sxs-lookup"><span data-stu-id="c03d5-187">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="c03d5-188">如果用户选择 `Defense` 交付分类 (`Classification`)，则验证需要交付说明 (`Description`) 的值。</span><span class="sxs-lookup"><span data-stu-id="c03d5-188">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="c03d5-189">在组件中设置验证消息时，它们将被添加到验证器的 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>，并在 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 中显示：</span><span class="sxs-lookup"><span data-stu-id="c03d5-189">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="c03d5-190">除了使用[验证组件](#validator-components)，还可使用数据注释验证属性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-190">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="c03d5-191">应用于窗体模型的自定义属性使用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件激活。</span><span class="sxs-lookup"><span data-stu-id="c03d5-191">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="c03d5-192">当与服务器端验证一起使用时，该属性都必须可在服务器上执行。</span><span class="sxs-lookup"><span data-stu-id="c03d5-192">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="c03d5-193">有关详细信息，请参阅 <xref:mvc/models/validation#alternatives-to-built-in-attributes>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-193">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="c03d5-194">服务器验证</span><span class="sxs-lookup"><span data-stu-id="c03d5-194">Server validation</span></span>

<span data-ttu-id="c03d5-195">服务器验证可通过服务器[验证器组件](#validator-components)完成：</span><span class="sxs-lookup"><span data-stu-id="c03d5-195">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="c03d5-196">使用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件处理窗体中的客户端验证。</span><span class="sxs-lookup"><span data-stu-id="c03d5-196">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="c03d5-197">当窗体传递客户端验证（调用 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>）时，将 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> 发送到后端服务器 API 进行窗体处理。</span><span class="sxs-lookup"><span data-stu-id="c03d5-197">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="c03d5-198">处理服务器上的模型验证。</span><span class="sxs-lookup"><span data-stu-id="c03d5-198">Process model validation on the server.</span></span>
* <span data-ttu-id="c03d5-199">服务器 API 包括开发人员提供的内置框架数据注释验证和自定义验证逻辑。</span><span class="sxs-lookup"><span data-stu-id="c03d5-199">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="c03d5-200">如果验证在服务器上传递，则处理窗格并发送回成功状态代码（200 - 正常）。</span><span class="sxs-lookup"><span data-stu-id="c03d5-200">If validation passes on the server, process the form and send back a success status code (*200 - OK*).</span></span> <span data-ttu-id="c03d5-201">如果验证失败，则返回失败状态代码（400 - 错误请求）和字段验证错误。</span><span class="sxs-lookup"><span data-stu-id="c03d5-201">If validation fails, return a failure status code (*400 - Bad Request*) and the field validation errors.</span></span>
* <span data-ttu-id="c03d5-202">成功时禁用窗体，否则显示错误。</span><span class="sxs-lookup"><span data-stu-id="c03d5-202">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="c03d5-203">下面的示例基于：</span><span class="sxs-lookup"><span data-stu-id="c03d5-203">The following example is based on:</span></span>

* <span data-ttu-id="c03d5-204">托管的 Blazor 解决方案，由 [Blazor 托管项目模板](xref:blazor/hosting-models#blazor-webassembly)创建。</span><span class="sxs-lookup"><span data-stu-id="c03d5-204">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="c03d5-205">此示例可与[安全性和 Identity 文档](xref:blazor/security/webassembly/index#implementation-guidance)中介绍的任何安全托管 Blazor 方案一起使用。</span><span class="sxs-lookup"><span data-stu-id="c03d5-205">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="c03d5-206">前面的[内置窗体组件](#built-in-forms-components)部分中的 Starfleet Starship 数据库窗体示例。</span><span class="sxs-lookup"><span data-stu-id="c03d5-206">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="c03d5-207">Blazor 框架的 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件。</span><span class="sxs-lookup"><span data-stu-id="c03d5-207">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="c03d5-208">[验证器组件](#validator-components)部分中显示的 `CustomValidator` 组件。</span><span class="sxs-lookup"><span data-stu-id="c03d5-208">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="c03d5-209">在下面的示例中，如果用户选择 `Defense` 交付分类 (`Classification`)，则服务器 API 将验证是否为交付说明 (`Description`) 提供了值。</span><span class="sxs-lookup"><span data-stu-id="c03d5-209">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="c03d5-210">将 `Starship` 模型放入解决方案的 `Shared` 项目中，以便客户端和服务器应用都可使用该模型。</span><span class="sxs-lookup"><span data-stu-id="c03d5-210">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="c03d5-211">模型需要数据注释，因此请将 [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) 的包引用添加到 `Shared` 项目的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="c03d5-211">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="c03d5-212">若要确定包的最新非预览版本，请前往 [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations) 查看包版本历史记录。</span><span class="sxs-lookup"><span data-stu-id="c03d5-212">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="c03d5-213">在服务器 API 项目中，添加控制器来处理 Starship 验证请求 (`Controllers/StarshipValidation.cs`) 并返回失败的验证消息：</span><span class="sxs-lookup"><span data-stu-id="c03d5-213">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace {ASSEMBLY NAME}.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

<span data-ttu-id="c03d5-214">在前面的示例中，占位符 `{ASSEMBLY NAME}` 是应用程序的程序集名称（例如 `BlazorSample.Server`）。</span><span class="sxs-lookup"><span data-stu-id="c03d5-214">In the preceding example, the placeholder `{ASSEMBLY NAME}` is the app's assembly name (for example, `BlazorSample.Server`).</span></span>

<span data-ttu-id="c03d5-215">当服务器上发生模型绑定验证错误时，[`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) 通常通过 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 返回[默认错误请求响应](xref:web-api/index#default-badrequest-response)。</span><span class="sxs-lookup"><span data-stu-id="c03d5-215">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="c03d5-216">如下例所示，当“Starfleet Starship 数据库”窗格的部分字段未提交且窗格未通过验证时，响应包含的数据不仅仅是验证错误：</span><span class="sxs-lookup"><span data-stu-id="c03d5-216">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

<span data-ttu-id="c03d5-217">如果服务器 API 返回前面的默认 JSON 响应，则客户端可分析响应以获取 `errors` 节点的子节点。</span><span class="sxs-lookup"><span data-stu-id="c03d5-217">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="c03d5-218">但是，分析文件不方便。</span><span class="sxs-lookup"><span data-stu-id="c03d5-218">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="c03d5-219">分析 JSON 需要调用 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 后的其他代码，以生成窗体验证错误处理的 [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) 错误。</span><span class="sxs-lookup"><span data-stu-id="c03d5-219">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="c03d5-220">理想情况下，服务器 API 应只返回验证错误：</span><span class="sxs-lookup"><span data-stu-id="c03d5-220">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="c03d5-221">若要修改服务器 API 的响应，使其仅返回验证错误，请更改在 `Startup.ConfigureServices` 中注释了 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> 的操作上调用的委托。</span><span class="sxs-lookup"><span data-stu-id="c03d5-221">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c03d5-222">对于 API 终结点 (`/StarshipValidation`)，返回具有 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> 的 <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-222">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="c03d5-223">对于任何其他 API 终结点，通过使用新的 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 返回对象结果来保留默认行为：</span><span class="sxs-lookup"><span data-stu-id="c03d5-223">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

<span data-ttu-id="c03d5-224">有关详细信息，请参阅 <xref:web-api/handle-errors#validation-failure-error-response>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-224">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="c03d5-225">在客户端项目中，添加[验证器组件](#validator-components)部分中显示的验证器组件。</span><span class="sxs-lookup"><span data-stu-id="c03d5-225">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="c03d5-226">在客户端项目中，更新“Starfleet Starship 数据库”窗体，以显示服务器验证错误和 `CustomValidator` 组件的帮助。</span><span class="sxs-lookup"><span data-stu-id="c03d5-226">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="c03d5-227">当服务器 API 返回验证消息时，这些消息将添加到 `CustomValidator` 组件的 <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-227">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="c03d5-228">此错误在窗体的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 中提供，以供窗体的 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 显示：</span><span class="sxs-lookup"><span data-stu-id="c03d5-228">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="c03d5-229">除了[验证组件](#validator-components)，还可使用数据注释验证属性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-229">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="c03d5-230">应用于窗体模型的自定义属性使用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件激活。</span><span class="sxs-lookup"><span data-stu-id="c03d5-230">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="c03d5-231">当与服务器端验证一起使用时，该属性都必须可在服务器上执行。</span><span class="sxs-lookup"><span data-stu-id="c03d5-231">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="c03d5-232">有关详细信息，请参阅 <xref:mvc/models/validation#alternatives-to-built-in-attributes>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-232">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="c03d5-233">本部分中的服务器端验证方法适用于本文档集中的所有 Blazor WebAssembly 托管解决方案示例：</span><span class="sxs-lookup"><span data-stu-id="c03d5-233">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="c03d5-234">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="c03d5-234">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="c03d5-235">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="c03d5-235">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="c03d5-236">Identity 服务器</span><span class="sxs-lookup"><span data-stu-id="c03d5-236">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="c03d5-237">基于输入事件的 InputText</span><span class="sxs-lookup"><span data-stu-id="c03d5-237">InputText based on the input event</span></span>

<span data-ttu-id="c03d5-238">使用 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件创建一个使用 `input` 事件而不是 `change` 事件的自定义组件。</span><span class="sxs-lookup"><span data-stu-id="c03d5-238">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="c03d5-239">在下面的示例中，`CustomInputText` 组件继承框架的 `InputText` 组件，并将事件绑定设置为 `oninput` 事件。</span><span class="sxs-lookup"><span data-stu-id="c03d5-239">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets event binding to the `oninput` event.</span></span>

<span data-ttu-id="c03d5-240">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="c03d5-240">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input @attributes="AdditionalAttributes" class="@CssClass" 
    @bind="CurrentValueAsString" @bind:event="oninput" />
```

<span data-ttu-id="c03d5-241">`CustomInputText` 组件可在任何使用 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 的位置使用：</span><span class="sxs-lookup"><span data-stu-id="c03d5-241">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="c03d5-242">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="c03d5-242">`Pages/TestForm.razor`:</span></span>

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="c03d5-243">单选按钮</span><span class="sxs-lookup"><span data-stu-id="c03d5-243">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c03d5-244">结合使用 `InputRadio` 组件和 `InputRadioGroup` 组件以创建单选按钮组。</span><span class="sxs-lookup"><span data-stu-id="c03d5-244">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="c03d5-245">在下面的示例中，将属性添加到[内置窗体组件](#built-in-forms-components)部分所述的 `Starship` 模型中：</span><span class="sxs-lookup"><span data-stu-id="c03d5-245">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

<span data-ttu-id="c03d5-246">将以下 `enums` 添加到应用。</span><span class="sxs-lookup"><span data-stu-id="c03d5-246">Add the following `enums` to the app.</span></span> <span data-ttu-id="c03d5-247">创建一个新文件来保存 `enums`，或将 `enums` 添加到 `Starship.cs` 文件中。</span><span class="sxs-lookup"><span data-stu-id="c03d5-247">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="c03d5-248">使 `Starship` 模型和 Starfleet Starship 数据库窗体都可访问 `enums`：</span><span class="sxs-lookup"><span data-stu-id="c03d5-248">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="c03d5-249">更新[内置窗体组件](#built-in-forms-components)部分所述的 Starfleet Starship 数据库窗体。</span><span class="sxs-lookup"><span data-stu-id="c03d5-249">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="c03d5-250">添加组件以生成：</span><span class="sxs-lookup"><span data-stu-id="c03d5-250">Add the components to produce:</span></span>

* <span data-ttu-id="c03d5-251">用于选择飞船制造商的单选按钮组。</span><span class="sxs-lookup"><span data-stu-id="c03d5-251">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="c03d5-252">用于选择飞船颜色和引擎的嵌套式单选按钮组。</span><span class="sxs-lookup"><span data-stu-id="c03d5-252">A nested radio button group for ship color and engine.</span></span>

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> <span data-ttu-id="c03d5-253">如果省略 `Name`，则 `InputRadio` 组件按其最新上级进行分组。</span><span class="sxs-lookup"><span data-stu-id="c03d5-253">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c03d5-254">使用窗体中的单选按钮时，数据绑定的处理方式与其他元素不同，因为单选按钮是作为一个组进行计算的。</span><span class="sxs-lookup"><span data-stu-id="c03d5-254">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="c03d5-255">每个单选按钮的值是固定的，但单选按钮组的值是所选单选按钮的值。</span><span class="sxs-lookup"><span data-stu-id="c03d5-255">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="c03d5-256">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="c03d5-256">The following example shows how to:</span></span>

* <span data-ttu-id="c03d5-257">处理单选按钮组的数据绑定。</span><span class="sxs-lookup"><span data-stu-id="c03d5-257">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="c03d5-258">使用自定义 `InputRadio` 组件支持验证。</span><span class="sxs-lookup"><span data-stu-id="c03d5-258">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="c03d5-259">以下 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 使用前面的 `InputRadio` 组件来获取和验证用户的评级：</span><span class="sxs-lookup"><span data-stu-id="c03d5-259">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="c03d5-260">将 `<select>` 元素选项绑定到 C# 对象 `null` 值</span><span class="sxs-lookup"><span data-stu-id="c03d5-260">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="c03d5-261">由于以下原因，没有将 `<select>` 元素选项值表示为 C# 对象 `null` 值的合理方法：</span><span class="sxs-lookup"><span data-stu-id="c03d5-261">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="c03d5-262">HTML 属性不能具有 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="c03d5-262">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="c03d5-263">HTML 中最接近的 `null` 等效项是 `<option>` 元素中缺少 HTML `value` 属性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-263">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="c03d5-264">选择没有 `value` 属性的 `<option>` 时，浏览器会将值视为该 `<option>` 的元素的 文本内容。</span><span class="sxs-lookup"><span data-stu-id="c03d5-264">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="c03d5-265">Blazor 框架不会尝试取消默认行为，因为这会涉及以下操作：</span><span class="sxs-lookup"><span data-stu-id="c03d5-265">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="c03d5-266">在框架中创建一系列特殊的解决办法。</span><span class="sxs-lookup"><span data-stu-id="c03d5-266">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="c03d5-267">对当前框架行为进行重大更改。</span><span class="sxs-lookup"><span data-stu-id="c03d5-267">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c03d5-268">HTML 中最合理的 `null` 等效项是空字符串 `value`。</span><span class="sxs-lookup"><span data-stu-id="c03d5-268">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="c03d5-269">Blazor 框架处理 `null` 到空字符串之间的转换，以便双向绑定到 `<select>` 的值。</span><span class="sxs-lookup"><span data-stu-id="c03d5-269">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c03d5-270">尝试双向绑定到 `<select>` 的值时，Blazor 框架不会自动处理 `null` 到空字符串之间的转换。</span><span class="sxs-lookup"><span data-stu-id="c03d5-270">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="c03d5-271">有关详细信息，请参阅[修复 `<select>` 到 null 值的绑定 (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221)。</span><span class="sxs-lookup"><span data-stu-id="c03d5-271">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="c03d5-272">验证支持</span><span class="sxs-lookup"><span data-stu-id="c03d5-272">Validation support</span></span>

<span data-ttu-id="c03d5-273"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件使用数据注释将验证支持附加到级联的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-273">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="c03d5-274">使用数据注释启用对验证的支持需要此显式手势。</span><span class="sxs-lookup"><span data-stu-id="c03d5-274">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="c03d5-275">若要使用不同于数据注释的验证系统，请用自定义实现替换 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-275">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="c03d5-276">可在以下参考源中检查 ASP.NET Core 的实现[`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)：</span><span class="sxs-lookup"><span data-stu-id="c03d5-276">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="c03d5-277">前面的参考源链接提供了来自存储库 `master` 分支的代码，该分支表示产品单元当前对 ASP.NET Core 下一版本的开发。</span><span class="sxs-lookup"><span data-stu-id="c03d5-277">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="c03d5-278">若要为其他版本选择分支，请使用 GitHub 分支选择器（例如 `release/3.1`）。</span><span class="sxs-lookup"><span data-stu-id="c03d5-278">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="c03d5-279">Blazor 执行两种类型的验证：</span><span class="sxs-lookup"><span data-stu-id="c03d5-279">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="c03d5-280">当用户从某个字段中跳出时，将执行 *字段验证*。</span><span class="sxs-lookup"><span data-stu-id="c03d5-280">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="c03d5-281">在字段验证期间，<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件将报告的所有验证结果与该字段相关联。</span><span class="sxs-lookup"><span data-stu-id="c03d5-281">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="c03d5-282">当用户提交窗体时，将执行 *模型验证*。</span><span class="sxs-lookup"><span data-stu-id="c03d5-282">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="c03d5-283">在模型验证期间，<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件尝试根据验证结果报告的成员名称来确定字段。</span><span class="sxs-lookup"><span data-stu-id="c03d5-283">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="c03d5-284">与单个成员无关的验证结果将与模型而不是字段相关联。</span><span class="sxs-lookup"><span data-stu-id="c03d5-284">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="c03d5-285">验证摘要和验证消息组件</span><span class="sxs-lookup"><span data-stu-id="c03d5-285">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="c03d5-286"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件用于汇总所有验证消息，这与[验证摘要标记帮助程序](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)类似：</span><span class="sxs-lookup"><span data-stu-id="c03d5-286">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="c03d5-287">使用 `Model` 参数输出特定模型的验证消息：</span><span class="sxs-lookup"><span data-stu-id="c03d5-287">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="c03d5-288"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 组件用于显示特定字段的验证消息，这与[验证消息标记帮助程序](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)类似。</span><span class="sxs-lookup"><span data-stu-id="c03d5-288">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="c03d5-289">使用 `For` 属性和一个为模型属性命名的 Lambda 表达式来指定要验证的字段：</span><span class="sxs-lookup"><span data-stu-id="c03d5-289">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="c03d5-290"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 和 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-290">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="c03d5-291">与某个组件参数不匹配的所有属性都将添加到生成的 `<div>` 或 `<ul>` 元素中。</span><span class="sxs-lookup"><span data-stu-id="c03d5-291">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="c03d5-292">在应用的样式表（`wwwroot/css/app.css` 或 `wwwroot/css/site.css`）中控制验证消息的样式。</span><span class="sxs-lookup"><span data-stu-id="c03d5-292">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="c03d5-293">默认 `validation-message` 类将验证消息的文本颜色设置为红色：</span><span class="sxs-lookup"><span data-stu-id="c03d5-293">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="c03d5-294">自定义验证属性</span><span class="sxs-lookup"><span data-stu-id="c03d5-294">Custom validation attributes</span></span>

<span data-ttu-id="c03d5-295">当使用[自定义验证属性](xref:mvc/models/validation#custom-attributes)时，为确保验证结果与字段正确关联，请在创建 <xref:System.ComponentModel.DataAnnotations.ValidationResult> 时传递验证上下文的 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>：</span><span class="sxs-lookup"><span data-stu-id="c03d5-295">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
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

> [!NOTE]
> <span data-ttu-id="c03d5-296"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> 为 `null`。</span><span class="sxs-lookup"><span data-stu-id="c03d5-296"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="c03d5-297">不支持在 `IsValid` 方法中注入用于验证的服务。</span><span class="sxs-lookup"><span data-stu-id="c03d5-297">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a><span data-ttu-id="c03d5-298">自定义验证类属性</span><span class="sxs-lookup"><span data-stu-id="c03d5-298">Custom validation class attributes</span></span>

<span data-ttu-id="c03d5-299">与 CSS 框架集成时，自定义验证类名称非常有用，例如[启动](https://getbootstrap.com/)。</span><span class="sxs-lookup"><span data-stu-id="c03d5-299">Custom validation class names are useful when integrating with CSS frameworks, such as [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="c03d5-300">若要指定自定义验证类名称，请创建从 `FieldCssClassProvider` 派生的类，并在 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 实例上设置该类：</span><span class="sxs-lookup"><span data-stu-id="c03d5-300">To specify custom validation class names, create a class derived from `FieldCssClassProvider` and set the class on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance:</span></span>

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="c03d5-301">Blazor 数据注释验证包</span><span class="sxs-lookup"><span data-stu-id="c03d5-301">Blazor data annotations validation package</span></span>

<span data-ttu-id="c03d5-302">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 是使用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件填补验证经验空白的包。</span><span class="sxs-lookup"><span data-stu-id="c03d5-302">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="c03d5-303">该包目前处于 *试验阶段*。</span><span class="sxs-lookup"><span data-stu-id="c03d5-303">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="c03d5-304">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 包具有最新版本的候选发布 ([Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation))。此时继续使用实验性候选发布包。</span><span class="sxs-lookup"><span data-stu-id="c03d5-304">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="c03d5-305">在将来的版本中，包的程序集可能会移动到框架或运行时。</span><span class="sxs-lookup"><span data-stu-id="c03d5-305">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="c03d5-306">请观看[公告 GitHub 存储库](https://github.com/aspnet/Announcements)、[dotnet/aspnetcore GitHub 存储库](https://github.com/dotnet/aspnetcore)或本主题部分，获取进一步更新。</span><span class="sxs-lookup"><span data-stu-id="c03d5-306">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

::: moniker range="< aspnetcore-5.0"

### <a name="compareproperty-attribute"></a><span data-ttu-id="c03d5-307">`[CompareProperty]` 特性</span><span class="sxs-lookup"><span data-stu-id="c03d5-307">`[CompareProperty]` attribute</span></span>

<span data-ttu-id="c03d5-308"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件，因为它不会将验证结果与特定成员关联。</span><span class="sxs-lookup"><span data-stu-id="c03d5-308">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="c03d5-309">这可能会导致字段级验证的行为与提交时整个模型的验证行为不一致。</span><span class="sxs-lookup"><span data-stu-id="c03d5-309">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="c03d5-310">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 试验性包引入了一个附加的验证属性 `ComparePropertyAttribute`，它可以克服这些限制。</span><span class="sxs-lookup"><span data-stu-id="c03d5-310">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="c03d5-311">在 Blazor 应用中，`[CompareProperty]` 可直接替代 [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-311">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

::: moniker-end

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="c03d5-312">嵌套模型、集合类型和复杂类型</span><span class="sxs-lookup"><span data-stu-id="c03d5-312">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="c03d5-313">Blazor 支持结合使用数据注释和内置的 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 来验证窗体输入。</span><span class="sxs-lookup"><span data-stu-id="c03d5-313">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="c03d5-314">但是，<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 仅验证绑定到窗体的模型的顶级属性（不包括集合类型或复杂类型的属性）。</span><span class="sxs-lookup"><span data-stu-id="c03d5-314">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="c03d5-315">若要验证绑定模型的整个对象图（包括集合类型和复杂类型的属性），请使用试验性 [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 包提供的 `ObjectGraphDataAnnotationsValidator`：</span><span class="sxs-lookup"><span data-stu-id="c03d5-315">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="c03d5-316">用 `[ValidateComplexType]` 注释模型属性。</span><span class="sxs-lookup"><span data-stu-id="c03d5-316">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="c03d5-317">在以下模型类中，`ShipDescription` 类包含附加数据注释，用于在将模型绑定到窗体时进行验证：</span><span class="sxs-lookup"><span data-stu-id="c03d5-317">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="c03d5-318">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="c03d5-318">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

<span data-ttu-id="c03d5-319">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="c03d5-319">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="c03d5-320">基于窗体验证启用提交按钮</span><span class="sxs-lookup"><span data-stu-id="c03d5-320">Enable the submit button based on form validation</span></span>

<span data-ttu-id="c03d5-321">若要基于窗体验证启用和禁用提交按钮，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c03d5-321">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="c03d5-322">使用窗体的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 在初始化组件时分配模型。</span><span class="sxs-lookup"><span data-stu-id="c03d5-322">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="c03d5-323">在上下文的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 回调中验证窗体，以启用和禁用提交按钮。</span><span class="sxs-lookup"><span data-stu-id="c03d5-323">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="c03d5-324">解除挂接 `Dispose` 方法中的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="c03d5-324">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="c03d5-325">有关详细信息，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-325">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="c03d5-326">使用 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 时，也不要将 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> 分配给 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-326">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="c03d5-327">在上面的示例中，如果满足以下条件，则将 `formInvalid` 设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="c03d5-327">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="c03d5-328">窗体已预加载有效的默认值。</span><span class="sxs-lookup"><span data-stu-id="c03d5-328">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="c03d5-329">你希望在加载窗体时启用提交按钮。</span><span class="sxs-lookup"><span data-stu-id="c03d5-329">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="c03d5-330">上述方法的副作用是在用户与任何一个字段进行交互后，<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件都会填充无效的字段。</span><span class="sxs-lookup"><span data-stu-id="c03d5-330">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="c03d5-331">可通过以下方式之一解决此情况：</span><span class="sxs-lookup"><span data-stu-id="c03d5-331">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="c03d5-332">不在窗体上使用 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件。</span><span class="sxs-lookup"><span data-stu-id="c03d5-332">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="c03d5-333">选择提交按钮时，使 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件可见（例如，在 `HandleValidSubmit` 方法中）。</span><span class="sxs-lookup"><span data-stu-id="c03d5-333">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="c03d5-334">疑难解答</span><span class="sxs-lookup"><span data-stu-id="c03d5-334">Troubleshoot</span></span>

> <span data-ttu-id="c03d5-335">InvalidOperationException：EditForm 需要 Model 参数或 EditContext 参数，但不能同时需要这两个参数。</span><span class="sxs-lookup"><span data-stu-id="c03d5-335">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="c03d5-336">确认 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 是否有 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> 或 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>。</span><span class="sxs-lookup"><span data-stu-id="c03d5-336">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="c03d5-337">不要对同一窗体使用这两者。</span><span class="sxs-lookup"><span data-stu-id="c03d5-337">Don't use both for the same form.</span></span>

<span data-ttu-id="c03d5-338">在将 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> 分配到窗体时，请确认模型类型是否已实例化，如下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="c03d5-338">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a><span data-ttu-id="c03d5-339">其他资源</span><span class="sxs-lookup"><span data-stu-id="c03d5-339">Additional resources</span></span>

* <xref:blazor/file-uploads>

::: moniker-end
