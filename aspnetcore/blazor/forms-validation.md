---
<span data-ttu-id="e616f-101">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-101">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-103">'Blazor'</span></span>
- <span data-ttu-id="e616f-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-104">'Identity'</span></span>
- <span data-ttu-id="e616f-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-106">'Razor'</span></span>
- <span data-ttu-id="e616f-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="e616f-108">ASP.NET Core Blazor 窗体和验证</span><span class="sxs-lookup"><span data-stu-id="e616f-108">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="e616f-109">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e616f-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e616f-110">在 Blazor 中，使用[数据注释](xref:mvc/models/validation)支持窗体和验证。</span><span class="sxs-lookup"><span data-stu-id="e616f-110">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="e616f-111">下面的 `ExampleModel` 类型使用数据注释定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="e616f-111">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="e616f-112">窗体是使用 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 组件定义的。</span><span class="sxs-lookup"><span data-stu-id="e616f-112">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="e616f-113">以下窗体展示了典型的元素、组件和 Razor 代码：</span><span class="sxs-lookup"><span data-stu-id="e616f-113">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
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

<span data-ttu-id="e616f-114">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="e616f-114">In the preceding example:</span></span>

* <span data-ttu-id="e616f-115">该窗体使用 `ExampleModel` 类型中定义的验证来验证 `name` 字段中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="e616f-115">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="e616f-116">该模型在组件的 `@code` 块中创建，并保存在私有字段 (`exampleModel`) 中。</span><span class="sxs-lookup"><span data-stu-id="e616f-116">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="e616f-117">该字段分配给 `<EditForm>` 元素的 `Model` 属性。</span><span class="sxs-lookup"><span data-stu-id="e616f-117">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="e616f-118"><xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件的 `@bind-Value` 进行以下绑定：</span><span class="sxs-lookup"><span data-stu-id="e616f-118">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="e616f-119">将模型属性 (`exampleModel.Name`) 绑定到 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件的 `Value` 属性。</span><span class="sxs-lookup"><span data-stu-id="e616f-119">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="e616f-120">有关属性绑定的详细信息，请参阅 <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>。</span><span class="sxs-lookup"><span data-stu-id="e616f-120">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="e616f-121">将更改事件委托绑定到 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件的 `ValueChanged` 属性。</span><span class="sxs-lookup"><span data-stu-id="e616f-121">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="e616f-122"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件使用数据注释附加验证支持。</span><span class="sxs-lookup"><span data-stu-id="e616f-122">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="e616f-123"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件汇总验证消息。</span><span class="sxs-lookup"><span data-stu-id="e616f-123">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="e616f-124">窗体成功提交（通过验证）时触发 `HandleValidSubmit`。</span><span class="sxs-lookup"><span data-stu-id="e616f-124">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="e616f-125">可使用一组内置的输入组件来接收和验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="e616f-125">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="e616f-126">当更改输入和提交窗体时，将验证输入。</span><span class="sxs-lookup"><span data-stu-id="e616f-126">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="e616f-127">下表显示了可用的输入组件。</span><span class="sxs-lookup"><span data-stu-id="e616f-127">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="e616f-128">输入组件</span><span class="sxs-lookup"><span data-stu-id="e616f-128">Input component</span></span> | <span data-ttu-id="e616f-129">呈现为&hellip;</span><span class="sxs-lookup"><span data-stu-id="e616f-129">Rendered as&hellip;</span></span> |
| ---
<span data-ttu-id="e616f-130">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-130">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-132">'Blazor'</span></span>
- <span data-ttu-id="e616f-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-133">'Identity'</span></span>
- <span data-ttu-id="e616f-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-135">'Razor'</span></span>
- <span data-ttu-id="e616f-136">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e616f-137">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-137">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-139">'Blazor'</span></span>
- <span data-ttu-id="e616f-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-140">'Identity'</span></span>
- <span data-ttu-id="e616f-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-142">'Razor'</span></span>
- <span data-ttu-id="e616f-143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e616f-144">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-144">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-146">'Blazor'</span></span>
- <span data-ttu-id="e616f-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-147">'Identity'</span></span>
- <span data-ttu-id="e616f-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-149">'Razor'</span></span>
- <span data-ttu-id="e616f-150">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e616f-151">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-151">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-153">'Blazor'</span></span>
- <span data-ttu-id="e616f-154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-154">'Identity'</span></span>
- <span data-ttu-id="e616f-155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-155">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-156">'Razor'</span></span>
- <span data-ttu-id="e616f-157">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e616f-158">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-158">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-160">'Blazor'</span></span>
- <span data-ttu-id="e616f-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-161">'Identity'</span></span>
- <span data-ttu-id="e616f-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-163">'Razor'</span></span>
- <span data-ttu-id="e616f-164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-164">'SignalR' uid:</span></span> 

<span data-ttu-id="e616f-165">-------- | --- title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-165">-------- | --- title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-167">'Blazor'</span></span>
- <span data-ttu-id="e616f-168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-168">'Identity'</span></span>
- <span data-ttu-id="e616f-169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-169">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-170">'Razor'</span></span>
- <span data-ttu-id="e616f-171">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e616f-172">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-172">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-174">'Blazor'</span></span>
- <span data-ttu-id="e616f-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-175">'Identity'</span></span>
- <span data-ttu-id="e616f-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-177">'Razor'</span></span>
- <span data-ttu-id="e616f-178">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e616f-179">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-179">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-181">'Blazor'</span></span>
- <span data-ttu-id="e616f-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-182">'Identity'</span></span>
- <span data-ttu-id="e616f-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-184">'Razor'</span></span>
- <span data-ttu-id="e616f-185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e616f-186">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-186">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-188">'Blazor'</span></span>
- <span data-ttu-id="e616f-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-189">'Identity'</span></span>
- <span data-ttu-id="e616f-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-191">'Razor'</span></span>
- <span data-ttu-id="e616f-192">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e616f-193">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-193">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-195">'Blazor'</span></span>
- <span data-ttu-id="e616f-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-196">'Identity'</span></span>
- <span data-ttu-id="e616f-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-198">'Razor'</span></span>
- <span data-ttu-id="e616f-199">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-199">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e616f-200">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-200">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-202">'Blazor'</span></span>
- <span data-ttu-id="e616f-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-203">'Identity'</span></span>
- <span data-ttu-id="e616f-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-205">'Razor'</span></span>
- <span data-ttu-id="e616f-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e616f-207">title:'ASP.NET Core Blazor 窗体和验证' author: description:'了解如何在 Blazor 中使用窗体和字段验证方案。'</span><span class="sxs-lookup"><span data-stu-id="e616f-207">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="e616f-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e616f-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e616f-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e616f-209">'Blazor'</span></span>
- <span data-ttu-id="e616f-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e616f-210">'Identity'</span></span>
- <span data-ttu-id="e616f-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e616f-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="e616f-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e616f-212">'Razor'</span></span>
- <span data-ttu-id="e616f-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e616f-213">'SignalR' uid:</span></span> 

<span data-ttu-id="e616f-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span><span class="sxs-lookup"><span data-stu-id="e616f-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span></span>

<span data-ttu-id="e616f-215">所有输入组件（包括 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>）都支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="e616f-215">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="e616f-216">与某个组件参数不匹配的所有属性都将添加到呈现的 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="e616f-216">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="e616f-217">输入组件为编辑时验证以及更改其 CSS 类以反映字段状态提供默认行为。</span><span class="sxs-lookup"><span data-stu-id="e616f-217">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="e616f-218">某些组件包含有用的分析逻辑。</span><span class="sxs-lookup"><span data-stu-id="e616f-218">Some components include useful parsing logic.</span></span> <span data-ttu-id="e616f-219">例如，<xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 和 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 通过将无法分析的值注册为验证错误，以恰当的方式来处理它们。</span><span class="sxs-lookup"><span data-stu-id="e616f-219">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="e616f-220">可接受 Null 值的类型也支持目标字段的为 Null 性（例如，`int?`）。</span><span class="sxs-lookup"><span data-stu-id="e616f-220">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="e616f-221">下面的 `Starship` 类型使用比之前的 `ExampleModel` 更大的属性和数据注释集来定义验证逻辑：</span><span class="sxs-lookup"><span data-stu-id="e616f-221">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="e616f-222">在上面的示例中，`Description` 是可选的，因为不存在任何数据注释。</span><span class="sxs-lookup"><span data-stu-id="e616f-222">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="e616f-223">以下窗体使用 `Starship` 模型中定义的验证来验证用户输入：</span><span class="sxs-lookup"><span data-stu-id="e616f-223">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
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
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="e616f-224"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> 创建一个 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 作为[级联值](xref:blazor/components#cascading-values-and-parameters)来跟踪有关编辑过程的元数据，其中包括已修改的字段和当前的验证消息。</span><span class="sxs-lookup"><span data-stu-id="e616f-224">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="e616f-225"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> 还为有效和无效的提交提供便捷的事件（<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>、<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>）。</span><span class="sxs-lookup"><span data-stu-id="e616f-225">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="e616f-226">或者，使用 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> 触发验证并使用自定义验证代码检查字段值。</span><span class="sxs-lookup"><span data-stu-id="e616f-226">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="e616f-227">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="e616f-227">In the following example:</span></span>

* <span data-ttu-id="e616f-228">选择“提交”按钮时，将运行 `HandleSubmit` 方法。</span><span class="sxs-lookup"><span data-stu-id="e616f-228">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="e616f-229">使用窗体的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 验证窗体。</span><span class="sxs-lookup"><span data-stu-id="e616f-229">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="e616f-230">通过将 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 传递给 `ServerValidate` 方法来进一步验证窗体，该方法会调用服务器上的 Web API 终结点（*未显示*）。</span><span class="sxs-lookup"><span data-stu-id="e616f-230">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="e616f-231">通过检查 `isValid` 获得客户端和服务器端验证的结果，并根据该结果运行其他代码。</span><span class="sxs-lookup"><span data-stu-id="e616f-231">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="e616f-232">基于输入事件的 InputText</span><span class="sxs-lookup"><span data-stu-id="e616f-232">InputText based on the input event</span></span>

<span data-ttu-id="e616f-233">使用 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 组件创建一个使用 `input` 事件而不是 `change` 事件的自定义组件。</span><span class="sxs-lookup"><span data-stu-id="e616f-233">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="e616f-234">使用以下标记创建一个组件，并像使用 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 一样使用该组件：</span><span class="sxs-lookup"><span data-stu-id="e616f-234">Create a component with the following markup, and use the component just as <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="e616f-235">使用单选按钮</span><span class="sxs-lookup"><span data-stu-id="e616f-235">Work with radio buttons</span></span>

<span data-ttu-id="e616f-236">使用窗体中的单选按钮时，数据绑定的处理方式与其他元素不同，因为单选按钮是作为一个组进行计算的。</span><span class="sxs-lookup"><span data-stu-id="e616f-236">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="e616f-237">每个单选按钮的值是固定的，但单选按钮组的值是所选单选按钮的值。</span><span class="sxs-lookup"><span data-stu-id="e616f-237">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="e616f-238">以下示例介绍如何：</span><span class="sxs-lookup"><span data-stu-id="e616f-238">The following example shows how to:</span></span>

* <span data-ttu-id="e616f-239">处理单选按钮组的数据绑定。</span><span class="sxs-lookup"><span data-stu-id="e616f-239">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="e616f-240">使用自定义 `InputRadio` 组件支持验证。</span><span class="sxs-lookup"><span data-stu-id="e616f-240">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="e616f-241">以下 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 使用前面的 `InputRadio` 组件来获取和验证用户的评级：</span><span class="sxs-lookup"><span data-stu-id="e616f-241">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
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
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a><span data-ttu-id="e616f-242">验证支持</span><span class="sxs-lookup"><span data-stu-id="e616f-242">Validation support</span></span>

<span data-ttu-id="e616f-243"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件使用数据注释将验证支持附加到级联的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>。</span><span class="sxs-lookup"><span data-stu-id="e616f-243">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="e616f-244">使用数据注释启用对验证的支持需要此显式手势。</span><span class="sxs-lookup"><span data-stu-id="e616f-244">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="e616f-245">若要使用不同于数据注释的验证系统，请用自定义实现替换 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>。</span><span class="sxs-lookup"><span data-stu-id="e616f-245">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="e616f-246">可在以下参考源中检查 ASP.NET Core 的实现：[DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="e616f-246">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="e616f-247"> 执行两种类型的验证：</span><span class="sxs-lookup"><span data-stu-id="e616f-247"> performs two types of validation:</span></span>

* <span data-ttu-id="e616f-248">当用户从某个字段中跳出时，将执行*字段验证*。</span><span class="sxs-lookup"><span data-stu-id="e616f-248">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="e616f-249">在字段验证期间，<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件将报告的所有验证结果与该字段相关联。</span><span class="sxs-lookup"><span data-stu-id="e616f-249">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="e616f-250">当用户提交窗体时，将执行*模型验证*。</span><span class="sxs-lookup"><span data-stu-id="e616f-250">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="e616f-251">在模型验证期间，<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件尝试根据验证结果报告的成员名称来确定字段。</span><span class="sxs-lookup"><span data-stu-id="e616f-251">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="e616f-252">与单个成员无关的验证结果将与模型而不是字段相关联。</span><span class="sxs-lookup"><span data-stu-id="e616f-252">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="e616f-253">验证摘要和验证消息组件</span><span class="sxs-lookup"><span data-stu-id="e616f-253">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="e616f-254"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件用于汇总所有验证消息，这与[验证摘要标记帮助程序](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)类似：</span><span class="sxs-lookup"><span data-stu-id="e616f-254">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="e616f-255">使用 `Model` 参数输出特定模型的验证消息：</span><span class="sxs-lookup"><span data-stu-id="e616f-255">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="e616f-256"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 组件用于显示特定字段的验证消息，这与[验证消息标记帮助程序](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)类似。</span><span class="sxs-lookup"><span data-stu-id="e616f-256">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="e616f-257">使用 `For` 属性和一个为模型属性命名的 Lambda 表达式来指定要验证的字段：</span><span class="sxs-lookup"><span data-stu-id="e616f-257">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="e616f-258"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 和 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件支持任意属性。</span><span class="sxs-lookup"><span data-stu-id="e616f-258">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="e616f-259">与某个组件参数不匹配的所有属性都将添加到生成的 `<div>` 或 `<ul>` 元素中。</span><span class="sxs-lookup"><span data-stu-id="e616f-259">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="e616f-260">自定义验证属性</span><span class="sxs-lookup"><span data-stu-id="e616f-260">Custom validation attributes</span></span>

<span data-ttu-id="e616f-261">当使用[自定义验证属性](xref:mvc/models/validation#custom-attributes)时，为确保验证结果与字段正确关联，请在创建 <xref:System.ComponentModel.DataAnnotations.ValidationResult> 时传递验证上下文的 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>：</span><span class="sxs-lookup"><span data-stu-id="e616f-261">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="e616f-262"> 数据注释验证包</span><span class="sxs-lookup"><span data-stu-id="e616f-262"> data annotations validation package</span></span>

<span data-ttu-id="e616f-263">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 是一个使用 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件填补验证体验缺口的验证包。</span><span class="sxs-lookup"><span data-stu-id="e616f-263">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="e616f-264">该包目前处于*试验阶段*。</span><span class="sxs-lookup"><span data-stu-id="e616f-264">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="e616f-265">[CompareProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="e616f-265">[CompareProperty] attribute</span></span>

<span data-ttu-id="e616f-266"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> 不适用于 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 组件，因为它不会将验证结果与特定成员关联。</span><span class="sxs-lookup"><span data-stu-id="e616f-266">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="e616f-267">这可能会导致字段级验证的行为与提交时整个模型的验证行为不一致。</span><span class="sxs-lookup"><span data-stu-id="e616f-267">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="e616f-268">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 试验性包引入了一个附加的验证属性 `ComparePropertyAttribute`，它可以克服这些限制。</span><span class="sxs-lookup"><span data-stu-id="e616f-268">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="e616f-269">在 Blazor 应用中，`[CompareProperty]` 可直接替代 [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="e616f-269">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="e616f-270">嵌套模型、集合类型和复杂类型</span><span class="sxs-lookup"><span data-stu-id="e616f-270">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="e616f-271"> 支持结合使用数据注释和内置的 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 来验证窗体输入。</span><span class="sxs-lookup"><span data-stu-id="e616f-271"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="e616f-272">但是，<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 仅验证绑定到窗体的模型的顶级属性（不包括集合类型或复杂类型的属性）。</span><span class="sxs-lookup"><span data-stu-id="e616f-272">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="e616f-273">若要验证绑定模型的整个对象图（包括集合类型和复杂类型的属性），请使用试验性 [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 包提供的 `ObjectGraphDataAnnotationsValidator`：</span><span class="sxs-lookup"><span data-stu-id="e616f-273">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="e616f-274">用 `[ValidateComplexType]` 注释模型属性。</span><span class="sxs-lookup"><span data-stu-id="e616f-274">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="e616f-275">在以下模型类中，`ShipDescription` 类包含附加数据注释，用于在将模型绑定到窗体时进行验证：</span><span class="sxs-lookup"><span data-stu-id="e616f-275">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="e616f-276">*Starship.cs*：</span><span class="sxs-lookup"><span data-stu-id="e616f-276">*Starship.cs*:</span></span>

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

<span data-ttu-id="e616f-277">*ShipDescription.cs*：</span><span class="sxs-lookup"><span data-stu-id="e616f-277">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="e616f-278">基于窗体验证启用提交按钮</span><span class="sxs-lookup"><span data-stu-id="e616f-278">Enable the submit button based on form validation</span></span>

<span data-ttu-id="e616f-279">若要基于窗体验证启用和禁用提交按钮，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e616f-279">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="e616f-280">使用窗体的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext> 在初始化组件时分配模型。</span><span class="sxs-lookup"><span data-stu-id="e616f-280">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="e616f-281">在上下文的 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 回调中验证窗体，以启用和禁用提交按钮。</span><span class="sxs-lookup"><span data-stu-id="e616f-281">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="e616f-282">解除挂接 `Dispose` 方法中的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="e616f-282">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="e616f-283">有关详细信息，请参阅 <xref:blazor/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="e616f-283">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
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

<span data-ttu-id="e616f-284">在上面的示例中，如果满足以下条件，则将 `formInvalid` 设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="e616f-284">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="e616f-285">窗体已预加载有效的默认值。</span><span class="sxs-lookup"><span data-stu-id="e616f-285">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="e616f-286">你希望在加载窗体时启用提交按钮。</span><span class="sxs-lookup"><span data-stu-id="e616f-286">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="e616f-287">上述方法的副作用是在用户与任何一个字段进行交互后，<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件都会填充无效的字段。</span><span class="sxs-lookup"><span data-stu-id="e616f-287">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="e616f-288">可通过以下方式之一解决此情况：</span><span class="sxs-lookup"><span data-stu-id="e616f-288">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="e616f-289">不在窗体上使用 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件。</span><span class="sxs-lookup"><span data-stu-id="e616f-289">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="e616f-290">选择提交按钮时，使 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 组件可见（例如，在 `HandleValidSubmit` 方法中）。</span><span class="sxs-lookup"><span data-stu-id="e616f-290">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
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

## <a name="troubleshoot"></a><span data-ttu-id="e616f-291">疑难解答</span><span class="sxs-lookup"><span data-stu-id="e616f-291">Troubleshoot</span></span>

> <span data-ttu-id="e616f-292">InvalidOperationException：EditForm 需要 Model 参数或 EditContext 参数，但不能同时需要这两个参数。</span><span class="sxs-lookup"><span data-stu-id="e616f-292">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="e616f-293">确认 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 是否有 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> 或 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>。</span><span class="sxs-lookup"><span data-stu-id="e616f-293">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="e616f-294">在将 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> 分配到窗体时，请确认模型类型是否已实例化，如下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="e616f-294">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
