---
title: 'ASP.NET Core :::no-loc(Blazor)::: 模板化组件'
author: guardrex
description: 了解模板化组件如何接受一个或多个 UI 模板作为参数，然后将其用作组件呈现逻辑的一部分。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
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
uid: blazor/components/templated-components
ms.openlocfilehash: ee38040a1edc8db43b8d5bf0202fd519b153ea04
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056239"
---
# <a name="aspnet-core-no-locblazor-templated-components"></a><span data-ttu-id="ccab5-103">ASP.NET Core :::no-loc(Blazor)::: 模板化组件</span><span class="sxs-lookup"><span data-stu-id="ccab5-103">ASP.NET Core :::no-loc(Blazor)::: templated components</span></span>

<span data-ttu-id="ccab5-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ccab5-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ccab5-105">模板化组件是接受一个或多个 UI 模板作为参数的组件，然后可将其用作组件呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="ccab5-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="ccab5-106">通过模板化组件，可以创作适用面更广、比常规组件更便于重复使用的组件。</span><span class="sxs-lookup"><span data-stu-id="ccab5-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="ccab5-107">下面是一些示例：</span><span class="sxs-lookup"><span data-stu-id="ccab5-107">A couple of examples include:</span></span>

* <span data-ttu-id="ccab5-108">表组件，用户可通过它指定表的标题、行和页脚的模板。</span><span class="sxs-lookup"><span data-stu-id="ccab5-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="ccab5-109">列表组件，用户可通过它指定用于呈现列表中项的模板。</span><span class="sxs-lookup"><span data-stu-id="ccab5-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

<span data-ttu-id="ccab5-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="ccab5-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="template-parameters"></a><span data-ttu-id="ccab5-111">模板参数</span><span class="sxs-lookup"><span data-stu-id="ccab5-111">Template parameters</span></span>

<span data-ttu-id="ccab5-112">通过指定一个或多个 <xref:Microsoft.AspNetCore.Components.RenderFragment> 或 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 类型的组件参数来定义模板化组件。</span><span class="sxs-lookup"><span data-stu-id="ccab5-112">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="ccab5-113">呈现片段，表示要呈现的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="ccab5-113">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="ccab5-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> 采用可在调用呈现片段时指定的类型参数。</span><span class="sxs-lookup"><span data-stu-id="ccab5-114"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="ccab5-115">`TableTemplate` 组件 (`TableTemplate.razor`)：</span><span class="sxs-lookup"><span data-stu-id="ccab5-115">`TableTemplate` component (`TableTemplate.razor`):</span></span>

[!code-razor[](../common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="ccab5-116">使用模板化组件时，可以使用与参数名称匹配的子元素（在以下示例中为 `TableHeader` 和 `RowTemplate`）指定模板参数：</span><span class="sxs-lookup"><span data-stu-id="ccab5-116">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { PetId = 2, Name = "Mr. Bigglesworth" },
        new Pet { PetId = 4, Name = "Salem Saberhagen" },
        new Pet { PetId = 7, Name = "K-9" }
    };

    private class Pet
    {
        public int PetId { get; set; }
        public string Name { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="ccab5-117">未来版本将支持泛型类型约束。</span><span class="sxs-lookup"><span data-stu-id="ccab5-117">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="ccab5-118">有关详细信息，请参阅[允许泛型类型约束 (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433)。</span><span class="sxs-lookup"><span data-stu-id="ccab5-118">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="ccab5-119">模板上下文参数</span><span class="sxs-lookup"><span data-stu-id="ccab5-119">Template context parameters</span></span>

<span data-ttu-id="ccab5-120">作为元素传递的 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 类型的组件实参具有一个名为 `context` 的隐式形参（例如前面的代码示例 `@context.PetId`），但可以使用子元素上的 `Context` 属性来更改形参名称。</span><span class="sxs-lookup"><span data-stu-id="ccab5-120">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="ccab5-121">在下面的示例中，`RowTemplate` 元素的 `Context` 属性指定了 `pet` 参数：</span><span class="sxs-lookup"><span data-stu-id="ccab5-121">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

<span data-ttu-id="ccab5-122">或者，可以在组件元素上指定 `Context` 属性。</span><span class="sxs-lookup"><span data-stu-id="ccab5-122">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="ccab5-123">指定的 `Context` 属性适用于所有指定的模板参数。</span><span class="sxs-lookup"><span data-stu-id="ccab5-123">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="ccab5-124">如果你想为隐式子内容指定内容参数名称（不包含任何包装子元素），这可能很有用。</span><span class="sxs-lookup"><span data-stu-id="ccab5-124">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="ccab5-125">在下面的示例中，`Context` 属性显示在 `TableTemplate` 元素上，并应用于所有模板参数：</span><span class="sxs-lookup"><span data-stu-id="ccab5-125">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>

@code {
    ...
}
```

## <a name="generic-typed-components"></a><span data-ttu-id="ccab5-126">泛型类型化组件</span><span class="sxs-lookup"><span data-stu-id="ccab5-126">Generic-typed components</span></span>

<span data-ttu-id="ccab5-127">模板化组件通常是泛型类型。</span><span class="sxs-lookup"><span data-stu-id="ccab5-127">Templated components are often generically typed.</span></span> <span data-ttu-id="ccab5-128">例如，泛型 `ListViewTemplate` 组件 (`ListViewTemplate.razor`) 可用于呈现 `IEnumerable<T>` 值。</span><span class="sxs-lookup"><span data-stu-id="ccab5-128">For example, a generic `ListViewTemplate` component (`ListViewTemplate.razor`) can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="ccab5-129">若要定义泛型组件，请使用 [`@typeparam`](xref:mvc/views/razor#typeparam) 指令指定类型参数：</span><span class="sxs-lookup"><span data-stu-id="ccab5-129">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="ccab5-130">使用泛型类型化组件时，将在可能的情况下推断类型参数：</span><span class="sxs-lookup"><span data-stu-id="ccab5-130">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    private List<Pet> pets = new List<Pet>
    {
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "Salem Saberhagen" },
        new Pet { Name = "K-9" }
    };

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="ccab5-131">否则，必须使用与类型参数的名称匹配的属性显式指定类型参数。</span><span class="sxs-lookup"><span data-stu-id="ccab5-131">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="ccab5-132">在下面的示例中，`TItem="Pet"` 指定类型：</span><span class="sxs-lookup"><span data-stu-id="ccab5-132">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>

@code {
    ...
}
```
