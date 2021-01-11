---
title: ASP.NET Core Blazor 组件虚拟化
author: guardrex
description: 了解如何在 ASP.NET Core Blazor 应用中使用组件虚拟化。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 920a23aee0d0555e93c829142700709d5881afd2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753084"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="cf19d-103">ASP.NET Core Blazor 组件虚拟化</span><span class="sxs-lookup"><span data-stu-id="cf19d-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="cf19d-104">作者：[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="cf19d-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="cf19d-105">使用 Blazor 框架的内置虚拟化支持提高组件呈现的感知性能。</span><span class="sxs-lookup"><span data-stu-id="cf19d-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="cf19d-106">虚拟化是一种技术，用于将 UI 呈现限制为仅当前可见的部分。</span><span class="sxs-lookup"><span data-stu-id="cf19d-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="cf19d-107">例如，当应用必须呈现项的长列表，并且在任何给定的时间只需要一小部分项可见时，虚拟化很有帮助。</span><span class="sxs-lookup"><span data-stu-id="cf19d-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="cf19d-108">Blazor 提供 `Virtualize` 组件，可用于向应用的组件添加虚拟化。</span><span class="sxs-lookup"><span data-stu-id="cf19d-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="cf19d-109">如果不使用虚拟化，典型列表可能会使用 C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环来呈现列表中的每一项：</span><span class="sxs-lookup"><span data-stu-id="cf19d-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="cf19d-110">如果列表包含数千项，则呈现该列表可能会花费较长时间。</span><span class="sxs-lookup"><span data-stu-id="cf19d-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="cf19d-111">用户可能会遇到明显的 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="cf19d-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="cf19d-112">与其一次性呈现列表中的所有项，不如将 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环替换为 `Virtualize` 组件，并使用 `Items` 指定固定的项源。</span><span class="sxs-lookup"><span data-stu-id="cf19d-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="cf19d-113">这样，将仅呈现当前可见的项：</span><span class="sxs-lookup"><span data-stu-id="cf19d-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="cf19d-114">如果未使用 `Context` 指定组件的上下文，请在项目内容模板中使用 `context` 值 (`@context.{PROPERTY}`)：</span><span class="sxs-lookup"><span data-stu-id="cf19d-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="cf19d-115">`Virtualize` 组件根据容器的高度和呈现的项的大小来计算要呈现的项数。</span><span class="sxs-lookup"><span data-stu-id="cf19d-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="cf19d-116">`Virtualize` 组件的项内容可以包括：</span><span class="sxs-lookup"><span data-stu-id="cf19d-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="cf19d-117">纯 HTML 和 Razor 代码，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="cf19d-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="cf19d-118">一个或多个 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="cf19d-118">One or more Razor components.</span></span>
* <span data-ttu-id="cf19d-119">HTML/Razor 和 Razor 组件的组合。</span><span class="sxs-lookup"><span data-stu-id="cf19d-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="cf19d-120">项提供程序委托</span><span class="sxs-lookup"><span data-stu-id="cf19d-120">Item provider delegate</span></span>

<span data-ttu-id="cf19d-121">如果不想将所有项加载到内存中，可向组件的 `ItemsProvider` 参数指定项提供程序委托方法，以按需异步检索请求的项：</span><span class="sxs-lookup"><span data-stu-id="cf19d-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="cf19d-122">项提供程序接收 `ItemsProviderRequest`，它指定从特定起始索引开始的请求项数。</span><span class="sxs-lookup"><span data-stu-id="cf19d-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="cf19d-123">然后，项提供程序在数据库或其他服务中检索请求的项，并以 `ItemsProviderResult<TItem>` 形式将这些项与项总数一起返回。</span><span class="sxs-lookup"><span data-stu-id="cf19d-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="cf19d-124">项提供程序可以选择按每个请求检索项，也可以将项缓存以便后续使用。</span><span class="sxs-lookup"><span data-stu-id="cf19d-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="cf19d-125">`Virtualize` 组件只能从其参数中接受一个项源，因此，请不要尝试在使用项提供程序的同时为 `Items` 分配集合。</span><span class="sxs-lookup"><span data-stu-id="cf19d-125">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="cf19d-126">如果同时分配两个，则在运行时设置组件的参数时，将引发 <xref:System.InvalidOperationException>。</span><span class="sxs-lookup"><span data-stu-id="cf19d-126">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="cf19d-127">以下示例从 `EmployeeService` 加载员工：</span><span class="sxs-lookup"><span data-stu-id="cf19d-127">The following example loads employees from an `EmployeeService`:</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a><span data-ttu-id="cf19d-128">占位符</span><span class="sxs-lookup"><span data-stu-id="cf19d-128">Placeholder</span></span>

<span data-ttu-id="cf19d-129">由于从远程数据源请求项可能需要一些时间，你可以选择呈现占位符 (`<Placeholder>...</Placeholder>`)，直到项数据可用：</span><span class="sxs-lookup"><span data-stu-id="cf19d-129">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="cf19d-130">项大小</span><span class="sxs-lookup"><span data-stu-id="cf19d-130">Item size</span></span>

<span data-ttu-id="cf19d-131">你可以使用 `ItemSize` 设置每个项的像素大小（默认值：50px）：</span><span class="sxs-lookup"><span data-stu-id="cf19d-131">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="cf19d-132">溢出扫描计数</span><span class="sxs-lookup"><span data-stu-id="cf19d-132">Overscan count</span></span>

<span data-ttu-id="cf19d-133">`OverscanCount` 确定在可见区域之前和之后呈现的额外项数。</span><span class="sxs-lookup"><span data-stu-id="cf19d-133">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="cf19d-134">此设置有助于降低滚动期间的呈现频率。</span><span class="sxs-lookup"><span data-stu-id="cf19d-134">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="cf19d-135">但是，值越大，页面中呈现的元素越多（默认值：3）：</span><span class="sxs-lookup"><span data-stu-id="cf19d-135">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="cf19d-136">状态更改</span><span class="sxs-lookup"><span data-stu-id="cf19d-136">State changes</span></span>

<span data-ttu-id="cf19d-137">当对 `Virtualize` 组件呈现的项进行更改时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以强制重新计算和重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="cf19d-137">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
