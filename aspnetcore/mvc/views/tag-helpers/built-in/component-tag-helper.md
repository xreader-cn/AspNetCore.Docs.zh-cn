---
title: ASP.NET Core 中的组件标记帮助程序
author: guardrex
ms.author: riande
description: '了解如何使用 ASP.NET Core 组件标记帮助程序 :::no-loc(Razor)::: 在页面和视图中呈现组件。'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595449"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="2083e-103">ASP.NET Core 中的组件标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="2083e-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="2083e-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2083e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2083e-105">先决条件</span><span class="sxs-lookup"><span data-stu-id="2083e-105">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2083e-106">按照 *配置* 部分中的指导进行操作：</span><span class="sxs-lookup"><span data-stu-id="2083e-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [:::no-loc(Blazor WebAssembly):::](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [:::no-loc(Blazor Server):::](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="2083e-107">按照本文的 *配置* 部分中的指导进行操作 <xref:blazor/components/prerendering-and-integration?pivots=server> 。</span><span class="sxs-lookup"><span data-stu-id="2083e-107">Follow the guidance in the *Configuration* section of the <xref:blazor/components/prerendering-and-integration?pivots=server> article.</span></span>

::: moniker-end

## <a name="component-tag-helper"></a><span data-ttu-id="2083e-108">组件标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="2083e-108">Component Tag Helper</span></span>

<span data-ttu-id="2083e-109">若要从页面或视图呈现组件，请使用 [组件标记帮助](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) 程序 (`<component>` 标记) 。</span><span class="sxs-lookup"><span data-stu-id="2083e-109">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="2083e-110">在 :::no-loc(Razor)::: :::no-loc(Razor)::: .net 5.0 或更高版本的 ASP.NET Core 中支持将组件集成到 *托管 :::no-loc(Blazor WebAssembly)::: 应用* 中的页面和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="2083e-110">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a *hosted :::no-loc(Blazor WebAssembly)::: app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="2083e-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="2083e-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="2083e-112">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="2083e-112">Is prerendered into the page.</span></span>
* <span data-ttu-id="2083e-113">在页面上呈现为静态 HTML，或者包含从用户代理启动 :::no-loc(Blazor)::: 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="2083e-113">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2083e-114">:::no-loc(Blazor WebAssembly)::: 应用呈现模式如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="2083e-114">:::no-loc(Blazor WebAssembly)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="2083e-115">呈现模式</span><span class="sxs-lookup"><span data-stu-id="2083e-115">Render Mode</span></span> | <span data-ttu-id="2083e-116">描述</span><span class="sxs-lookup"><span data-stu-id="2083e-116">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="2083e-117">呈现应用程序的标记，以便在 :::no-loc(Blazor WebAssembly)::: 浏览器中加载时包含交互组件。</span><span class="sxs-lookup"><span data-stu-id="2083e-117">Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="2083e-118">组件未预呈现。</span><span class="sxs-lookup"><span data-stu-id="2083e-118">The component isn't prerendered.</span></span> <span data-ttu-id="2083e-119">此选项使你可以更轻松地 :::no-loc(Blazor WebAssembly)::: 在不同的页面上呈现不同的组件。</span><span class="sxs-lookup"><span data-stu-id="2083e-119">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="2083e-120">将组件 Prerenders 为静态 HTML，并包含 :::no-loc(Blazor WebAssembly)::: 应用的标记，以便在浏览器中加载时使组件成为交互组件。</span><span class="sxs-lookup"><span data-stu-id="2083e-120">Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="2083e-121">:::no-loc(Blazor Server)::: 应用呈现模式如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="2083e-121">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="2083e-122">呈现模式</span><span class="sxs-lookup"><span data-stu-id="2083e-122">Render Mode</span></span> | <span data-ttu-id="2083e-123">描述</span><span class="sxs-lookup"><span data-stu-id="2083e-123">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="2083e-124">在静态 HTML 中呈现组件，并包含 :::no-loc(Blazor Server)::: 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="2083e-124">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="2083e-125">用户代理启动时，此标记用于启动 :::no-loc(Blazor)::: 应用。</span><span class="sxs-lookup"><span data-stu-id="2083e-125">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="2083e-126">呈现 :::no-loc(Blazor Server)::: 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="2083e-126">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="2083e-127">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="2083e-127">Output from the component isn't included.</span></span> <span data-ttu-id="2083e-128">用户代理启动时，此标记用于启动 :::no-loc(Blazor)::: 应用。</span><span class="sxs-lookup"><span data-stu-id="2083e-128">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="2083e-129">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="2083e-129">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="2083e-130">:::no-loc(Blazor Server)::: 应用呈现模式如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="2083e-130">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="2083e-131">呈现模式</span><span class="sxs-lookup"><span data-stu-id="2083e-131">Render Mode</span></span> | <span data-ttu-id="2083e-132">描述</span><span class="sxs-lookup"><span data-stu-id="2083e-132">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="2083e-133">在静态 HTML 中呈现组件，并包含 :::no-loc(Blazor Server)::: 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="2083e-133">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="2083e-134">用户代理启动时，此标记用于启动 :::no-loc(Blazor)::: 应用。</span><span class="sxs-lookup"><span data-stu-id="2083e-134">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="2083e-135">呈现 :::no-loc(Blazor Server)::: 应用的标记。</span><span class="sxs-lookup"><span data-stu-id="2083e-135">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="2083e-136">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="2083e-136">Output from the component isn't included.</span></span> <span data-ttu-id="2083e-137">用户代理启动时，此标记用于启动 :::no-loc(Blazor)::: 应用。</span><span class="sxs-lookup"><span data-stu-id="2083e-137">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="2083e-138">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="2083e-138">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="2083e-139">其他特征包括：</span><span class="sxs-lookup"><span data-stu-id="2083e-139">Additional characteristics include:</span></span>

* <span data-ttu-id="2083e-140">允许多个组件标记帮助器呈现多个 :::no-loc(Razor)::: 组件。</span><span class="sxs-lookup"><span data-stu-id="2083e-140">Multiple Component Tag Helpers rendering multiple :::no-loc(Razor)::: components is allowed.</span></span>
* <span data-ttu-id="2083e-141">启动应用后，无法动态呈现组件。</span><span class="sxs-lookup"><span data-stu-id="2083e-141">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="2083e-142">尽管页面和视图可以使用组件，但不是这样。</span><span class="sxs-lookup"><span data-stu-id="2083e-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="2083e-143">组件不能使用视图和页特定的功能，如分部视图和节。</span><span class="sxs-lookup"><span data-stu-id="2083e-143">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="2083e-144">若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解为一个组件。</span><span class="sxs-lookup"><span data-stu-id="2083e-144">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="2083e-145">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="2083e-145">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="2083e-146">以下组件标记帮助程序使用以下组件在 `Counter` 应用程序的页面或视图中呈现组件 :::no-loc(Blazor Server)::: `ServerPrerendered` ：</span><span class="sxs-lookup"><span data-stu-id="2083e-146">The following Component Tag Helper renders the `Counter` component in a page or view in a :::no-loc(Blazor Server)::: app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="2083e-147">前面的示例假定 `Counter` 组件位于应用的 *Pages* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2083e-147">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="2083e-148">占位符 `{APP ASSEMBLY}` 是应用程序的程序集名称 (例如， `@using :::no-loc(Blazor):::Sample.Pages` 或 `@using :::no-loc(Blazor):::Sample.Client.Pages` 在托管解决方案中 :::no-loc(Blazor):::) 。</span><span class="sxs-lookup"><span data-stu-id="2083e-148">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages` or `@using :::no-loc(Blazor):::Sample.Client.Pages` in a hosted :::no-loc(Blazor)::: solution).</span></span>

<span data-ttu-id="2083e-149">组件标记帮助器还可以将参数传递给组件。</span><span class="sxs-lookup"><span data-stu-id="2083e-149">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="2083e-150">请考虑以下 `ColorfulCheckbox` 用于设置复选框标签颜色和大小的组件：</span><span class="sxs-lookup"><span data-stu-id="2083e-150">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying :::no-loc(Blazor):::?
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

<span data-ttu-id="2083e-151">`Size` `int` `Color` `string` 组件标记帮助器可以设置 () 和 () [组件参数](xref:blazor/components/index#component-parameters)：</span><span class="sxs-lookup"><span data-stu-id="2083e-151">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="2083e-152">前面的示例假定 `ColorfulCheckbox` 组件位于应用的 *共享* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2083e-152">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="2083e-153">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `@using :::no-loc(Blazor):::Sample.Shared`）。</span><span class="sxs-lookup"><span data-stu-id="2083e-153">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="2083e-154">在页面或视图中呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="2083e-154">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="2083e-155">传递带引号的字符串需要 [显式 :::no-loc(Razor)::: 表达式](xref:mvc/views/razor#explicit-razor-expressions)，如 `param-Color` 前面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="2083e-155">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="2083e-156">:::no-loc(Razor)::: `string` 由于属性是类型，因此类型值的分析行为不适用于 `param-*` 特性 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2083e-156">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="2083e-157">支持所有类型的参数，但除外：</span><span class="sxs-lookup"><span data-stu-id="2083e-157">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="2083e-158">泛型参数。</span><span class="sxs-lookup"><span data-stu-id="2083e-158">Generic parameters.</span></span>
* <span data-ttu-id="2083e-159">不可序列化参数。</span><span class="sxs-lookup"><span data-stu-id="2083e-159">Non-serializable parameters.</span></span>
* <span data-ttu-id="2083e-160">集合参数中的继承。</span><span class="sxs-lookup"><span data-stu-id="2083e-160">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="2083e-161">其类型在 :::no-loc(Blazor WebAssembly)::: 应用外或延迟加载的程序集中定义的参数。</span><span class="sxs-lookup"><span data-stu-id="2083e-161">Parameters whose type is defined outside of the :::no-loc(Blazor WebAssembly)::: app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="2083e-162">参数类型必须是 JSON 可序列化的，这通常意味着该类型必须具有默认的构造函数和可设置的属性。</span><span class="sxs-lookup"><span data-stu-id="2083e-162">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="2083e-163">例如，你可以 `Size` `Color` 在前面的示例中指定和的值，因为和的类型 `Size` `Color` 是 (`int` 和 `string`) （JSON 序列化程序支持的）的基元类型。</span><span class="sxs-lookup"><span data-stu-id="2083e-163">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="2083e-164">在下面的示例中，将类对象传递到组件：</span><span class="sxs-lookup"><span data-stu-id="2083e-164">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="2083e-165">*MyClass.cs* ：</span><span class="sxs-lookup"><span data-stu-id="2083e-165">*MyClass.cs* :</span></span>

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

<span data-ttu-id="2083e-166">**该类必须具有公共的无参数构造函数。**</span><span class="sxs-lookup"><span data-stu-id="2083e-166">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="2083e-167">*Shared/MyComponent* ：</span><span class="sxs-lookup"><span data-stu-id="2083e-167">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="2083e-168">*Pages/m y* ：</span><span class="sxs-lookup"><span data-stu-id="2083e-168">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="2083e-169">前面的示例假定 `MyComponent` 组件位于应用的 *共享* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2083e-169">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="2083e-170">占位符 `{APP ASSEMBLY}` 是应用程序的程序集名称 (例如， `@using :::no-loc(Blazor):::Sample` `@using :::no-loc(Blazor):::Sample.Shared`) 。</span><span class="sxs-lookup"><span data-stu-id="2083e-170">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="2083e-171">`MyClass` 在应用的命名空间中。</span><span class="sxs-lookup"><span data-stu-id="2083e-171">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2083e-172">其他资源</span><span class="sxs-lookup"><span data-stu-id="2083e-172">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
