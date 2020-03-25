---
title: ASP.NET Core 中的组件标记帮助程序
author: guardrex
ms.author: riande
description: 了解如何使用 ASP.NET Core 组件标记帮助程序在页面和视图中呈现 Razor 组件。
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 801ceb73de5bb4ef7500624e1fbddbf96d1ab89c
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226380"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="d8f7b-103">ASP.NET Core 中的组件标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="d8f7b-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="d8f7b-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d8f7b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d8f7b-105">若要从页面或视图呈现组件，请使用[组件标记帮助器](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

<span data-ttu-id="d8f7b-106">以下组件标记帮助器呈现页面或视图中的 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="d8f7b-106">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="d8f7b-107">组件标记帮助器还可以将参数传递给组件。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-107">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="d8f7b-108">请考虑以下 `ColorfulCheckbox` 组件，该组件设置复选框标签的颜色和大小：</span><span class="sxs-lookup"><span data-stu-id="d8f7b-108">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="d8f7b-109">`Size` （`int`）和 `Color` （`string`）[组件参数](xref:blazor/components#component-parameters)可由组件标记帮助器设置：</span><span class="sxs-lookup"><span data-stu-id="d8f7b-109">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="d8f7b-110">在页面或视图中呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="d8f7b-110">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="d8f7b-111">传递带引号的字符串需要[显式 Razor 表达式](xref:mvc/views/razor#explicit-razor-expressions)，如前面的示例中 `param-Color` 所示。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-111">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="d8f7b-112">`string` 类型值的 Razor 分析行为不适用于 `param-*` 属性，因为该属性是 `object` 类型。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-112">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="d8f7b-113">参数类型必须是 JSON 可序列化的，这通常意味着类型必须具有默认构造函数和可设置的属性。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-113">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="d8f7b-114">例如，你可以在前面的示例中为 `Size` 和 `Color` 指定一个值，因为 `Size` 和 `Color` 的类型是 JSON 序列化程序支持的基元类型（`int` 和 `string`）。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-114">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="d8f7b-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="d8f7b-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="d8f7b-116">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-116">Is prerendered into the page.</span></span>
* <span data-ttu-id="d8f7b-117">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-117">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="d8f7b-118">呈现模式</span><span class="sxs-lookup"><span data-stu-id="d8f7b-118">Render Mode</span></span> | <span data-ttu-id="d8f7b-119">说明</span><span class="sxs-lookup"><span data-stu-id="d8f7b-119">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="d8f7b-120">将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-120">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="d8f7b-121">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-121">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="d8f7b-122">呈现 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-122">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="d8f7b-123">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-123">Output from the component isn't included.</span></span> <span data-ttu-id="d8f7b-124">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-124">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="d8f7b-125">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-125">Renders the component into static HTML.</span></span> |

<span data-ttu-id="d8f7b-126">尽管页面和视图可以使用组件，但反过来则不行。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-126">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="d8f7b-127">组件不能使用视图和页特定的功能，如分部视图和节。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-127">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="d8f7b-128">若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解为一个组件。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-128">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="d8f7b-129">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="d8f7b-129">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8f7b-130">其他资源</span><span class="sxs-lookup"><span data-stu-id="d8f7b-130">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
