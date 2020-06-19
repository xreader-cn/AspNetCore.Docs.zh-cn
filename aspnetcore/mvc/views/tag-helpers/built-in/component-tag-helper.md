---
title: ASP.NET Core 中的组件标记帮助程序
author: guardrex
ms.author: riande
description: 了解如何使用 ASP.NET Core 组件标记帮助程序 Razor 在页面和视图中呈现组件。
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: df978d49201ba1010ddf13b1b9a63ae27116616e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103091"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="37f95-103">ASP.NET Core 中的组件标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="37f95-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="37f95-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="37f95-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="37f95-105">若要从页面或视图呈现组件，请使用[组件标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)。</span><span class="sxs-lookup"><span data-stu-id="37f95-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="37f95-106">先决条件</span><span class="sxs-lookup"><span data-stu-id="37f95-106">Prerequisites</span></span>

<span data-ttu-id="37f95-107">按照本文中 "*准备应用程序以使用组件" 页和 "视图*" 部分中的指导进行操作 <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> 。</span><span class="sxs-lookup"><span data-stu-id="37f95-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="37f95-108">组件标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="37f95-108">Component Tag Helper</span></span>

<span data-ttu-id="37f95-109">以下组件标记帮助程序 `Counter` 在页面或视图中呈现组件：</span><span class="sxs-lookup"><span data-stu-id="37f95-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="37f95-110">前面的示例假定 `Counter` 组件位于应用的*Pages*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="37f95-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="37f95-111">占位符 `{APP ASSEMBLY}` 是应用程序的程序集名称（例如 `@using BlazorSample.Pages` ）。</span><span class="sxs-lookup"><span data-stu-id="37f95-111">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages`).</span></span>

<span data-ttu-id="37f95-112">组件标记帮助器还可以将参数传递给组件。</span><span class="sxs-lookup"><span data-stu-id="37f95-112">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="37f95-113">请考虑以下 `ColorfulCheckbox` 用于设置复选框标签颜色和大小的组件：</span><span class="sxs-lookup"><span data-stu-id="37f95-113">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="37f95-114">`Size`（ `int` ）和 `Color` （ `string` ）[组件参数](xref:blazor/components/index#component-parameters)可由组件标记帮助器设置：</span><span class="sxs-lookup"><span data-stu-id="37f95-114">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="37f95-115">前面的示例假定 `ColorfulCheckbox` 组件位于应用的*共享*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="37f95-115">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="37f95-116">占位符 `{APP ASSEMBLY}` 是应用程序的程序集名称（例如 `@using BlazorSample.Shared` ）。</span><span class="sxs-lookup"><span data-stu-id="37f95-116">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="37f95-117">在页面或视图中呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="37f95-117">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="37f95-118">传递带引号的字符串需要[显式 Razor 表达式](xref:mvc/views/razor#explicit-razor-expressions)，如 `param-Color` 前面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="37f95-118">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="37f95-119">Razor `string` 由于属性是类型，因此类型值的分析行为不适用于 `param-*` 特性 `object` 。</span><span class="sxs-lookup"><span data-stu-id="37f95-119">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="37f95-120">参数类型必须是 JSON 可序列化的，这通常意味着该类型必须具有默认的构造函数和可设置的属性。</span><span class="sxs-lookup"><span data-stu-id="37f95-120">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="37f95-121">例如，在前面的示例中，可以为和指定一个值， `Size` `Color` 因为和的 `Size` 类型 `Color` 为基元类型（ `int` 和 `string` ），这些类型是 JSON 序列化程序支持的。</span><span class="sxs-lookup"><span data-stu-id="37f95-121">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="37f95-122">在下面的示例中，将类对象传递到组件：</span><span class="sxs-lookup"><span data-stu-id="37f95-122">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="37f95-123">*MyClass.cs*：</span><span class="sxs-lookup"><span data-stu-id="37f95-123">*MyClass.cs*:</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="37f95-124">**该类必须具有公共的无参数构造函数。**</span><span class="sxs-lookup"><span data-stu-id="37f95-124">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="37f95-125">*Shared/MyComponent*：</span><span class="sxs-lookup"><span data-stu-id="37f95-125">*Shared/MyComponent.razor*:</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="37f95-126">*Pages/m y*：</span><span class="sxs-lookup"><span data-stu-id="37f95-126">*Pages/MyPage.cshtml*:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="37f95-127">前面的示例假定 `MyComponent` 组件位于应用的*共享*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="37f95-127">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="37f95-128">占位符 `{APP ASSEMBLY}` 是应用程序的程序集名称（例如， `@using BlazorSample` 和 `@using BlazorSample.Shared` ）。</span><span class="sxs-lookup"><span data-stu-id="37f95-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="37f95-129">`MyClass`在应用的命名空间中。</span><span class="sxs-lookup"><span data-stu-id="37f95-129">`MyClass` is in the app's namespace.</span></span>

<span data-ttu-id="37f95-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="37f95-130"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="37f95-131">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="37f95-131">Is prerendered into the page.</span></span>
* <span data-ttu-id="37f95-132">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="37f95-132">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="37f95-133">呈现模式</span><span class="sxs-lookup"><span data-stu-id="37f95-133">Render Mode</span></span> | <span data-ttu-id="37f95-134">描述</span><span class="sxs-lookup"><span data-stu-id="37f95-134">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="37f95-135">将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="37f95-135">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="37f95-136">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="37f95-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="37f95-137">呈现 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="37f95-137">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="37f95-138">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="37f95-138">Output from the component isn't included.</span></span> <span data-ttu-id="37f95-139">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="37f95-139">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="37f95-140">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="37f95-140">Renders the component into static HTML.</span></span> |

<span data-ttu-id="37f95-141">尽管页面和视图可以使用组件，但不是这样。</span><span class="sxs-lookup"><span data-stu-id="37f95-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="37f95-142">组件不能使用视图和页特定的功能，如分部视图和节。</span><span class="sxs-lookup"><span data-stu-id="37f95-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="37f95-143">若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解为一个组件。</span><span class="sxs-lookup"><span data-stu-id="37f95-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="37f95-144">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="37f95-144">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37f95-145">其他资源</span><span class="sxs-lookup"><span data-stu-id="37f95-145">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
