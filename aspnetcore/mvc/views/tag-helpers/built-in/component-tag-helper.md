---
title: ASP.NET核心中的组件标记帮助程序
author: guardrex
ms.author: riande
description: 了解如何使用ASP.NET核心组件标记帮助器在页面和视图中呈现 Razor 组件。
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123433"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="3eefc-103">ASP.NET核心中的组件标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="3eefc-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="3eefc-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3eefc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3eefc-105">要从页面或视图呈现组件，请使用[组件标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)。</span><span class="sxs-lookup"><span data-stu-id="3eefc-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3eefc-106">先决条件</span><span class="sxs-lookup"><span data-stu-id="3eefc-106">Prerequisites</span></span>

<span data-ttu-id="3eefc-107">按照"准备应用"中的指南在<xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views>文章的*页面和视图部分使用组件*。</span><span class="sxs-lookup"><span data-stu-id="3eefc-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="3eefc-108">组件标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="3eefc-108">Component Tag Helper</span></span>

<span data-ttu-id="3eefc-109">以下组件标记帮助程序在页面或视图中`Counter`呈现组件：</span><span class="sxs-lookup"><span data-stu-id="3eefc-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="3eefc-110">前面的示例假定组件`Counter`位于应用的 *"主页"* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="3eefc-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="3eefc-111">组件标记帮助程序还可以将参数传递给组件。</span><span class="sxs-lookup"><span data-stu-id="3eefc-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="3eefc-112">请考虑设置复选框`ColorfulCheckbox`标签颜色和大小的以下组件：</span><span class="sxs-lookup"><span data-stu-id="3eefc-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="3eefc-113">`Size` （`int`）`Color`和`string`（ ）[组件参数](xref:blazor/components#component-parameters)可以由组件标记帮助程序设置：</span><span class="sxs-lookup"><span data-stu-id="3eefc-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="3eefc-114">前面的示例假定组件`ColorfulCheckbox`位于应用的 *"共享"* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="3eefc-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="3eefc-115">以下 HTML 呈现在页面或视图中：</span><span class="sxs-lookup"><span data-stu-id="3eefc-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="3eefc-116">传递引用的字符串需要[显式 Razor 表达式](xref:mvc/views/razor#explicit-razor-expressions)，如前面的`param-Color`示例所示。</span><span class="sxs-lookup"><span data-stu-id="3eefc-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="3eefc-117">`string`类型值的 Razor 解析行为不适用于属性，`param-*`因为属性是一种`object`类型。</span><span class="sxs-lookup"><span data-stu-id="3eefc-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="3eefc-118">参数类型必须是 JSON 可序列化的，这通常意味着类型必须具有默认构造函数和可设置属性。</span><span class="sxs-lookup"><span data-stu-id="3eefc-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="3eefc-119">例如，`Size`您可以为 和 在前面的示例中`Color`指定值，因为 和`Size``Color`的类型是基元类型`int`（`string`和 ），JSON 序列化器支持。</span><span class="sxs-lookup"><span data-stu-id="3eefc-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="3eefc-120">在下面的示例中，类对象传递给组件：</span><span class="sxs-lookup"><span data-stu-id="3eefc-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="3eefc-121">*MyClass.cs*：</span><span class="sxs-lookup"><span data-stu-id="3eefc-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="3eefc-122">**类必须具有公共无参数构造函数。**</span><span class="sxs-lookup"><span data-stu-id="3eefc-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="3eefc-123">*共享/我的组件.razor*：</span><span class="sxs-lookup"><span data-stu-id="3eefc-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="3eefc-124">*页面/MyPage.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="3eefc-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="3eefc-125">前面的示例假定组件`MyComponent`位于应用的 *"共享"* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="3eefc-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="3eefc-126">`MyClass`在应用的命名空间中 。`{APP ASSEMBLY}`</span><span class="sxs-lookup"><span data-stu-id="3eefc-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="3eefc-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="3eefc-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="3eefc-128">预置到页面中。</span><span class="sxs-lookup"><span data-stu-id="3eefc-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="3eefc-129">在页面上呈现为静态 HTML，或者如果它包含从用户代理引导 Blazor 应用的必要信息。</span><span class="sxs-lookup"><span data-stu-id="3eefc-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="3eefc-130">渲染模式</span><span class="sxs-lookup"><span data-stu-id="3eefc-130">Render Mode</span></span> | <span data-ttu-id="3eefc-131">说明</span><span class="sxs-lookup"><span data-stu-id="3eefc-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="3eefc-132">将组件呈现为静态 HTML，并包含服务器应用的Blazor标记。</span><span class="sxs-lookup"><span data-stu-id="3eefc-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3eefc-133">当用户代理启动时，此标记用于引导Blazor应用。</span><span class="sxs-lookup"><span data-stu-id="3eefc-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="3eefc-134">渲染服务器应用的Blazor标记。</span><span class="sxs-lookup"><span data-stu-id="3eefc-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3eefc-135">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="3eefc-135">Output from the component isn't included.</span></span> <span data-ttu-id="3eefc-136">当用户代理启动时，此标记用于引导Blazor应用。</span><span class="sxs-lookup"><span data-stu-id="3eefc-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="3eefc-137">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="3eefc-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="3eefc-138">虽然页面和视图可以使用组件，但事实并非如此。</span><span class="sxs-lookup"><span data-stu-id="3eefc-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="3eefc-139">组件无法使用特定于视图和页面的功能，如部分视图和节。</span><span class="sxs-lookup"><span data-stu-id="3eefc-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="3eefc-140">要使用组件中部分视图的逻辑，将部分视图逻辑分解到组件中。</span><span class="sxs-lookup"><span data-stu-id="3eefc-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="3eefc-141">不支持从静态 HTML 页呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="3eefc-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3eefc-142">其他资源</span><span class="sxs-lookup"><span data-stu-id="3eefc-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
