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
ms.openlocfilehash: afd2da19641b41871f06426934c39348daa54b1f
ms.sourcegitcommit: 2fea9bfe6127bbbdbb438406c82529b2bc331944
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065527"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="0119b-103">ASP.NET Core Blazor 组件虚拟化</span><span class="sxs-lookup"><span data-stu-id="0119b-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="0119b-104">作者：[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="0119b-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="0119b-105">使用 Blazor 框架的内置虚拟化支持提高组件呈现的感知性能。</span><span class="sxs-lookup"><span data-stu-id="0119b-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="0119b-106">虚拟化是一种技术，用于将 UI 呈现限制为仅当前可见的部分。</span><span class="sxs-lookup"><span data-stu-id="0119b-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="0119b-107">例如，当应用必须呈现项的长列表，并且在任何给定的时间只需要一小部分项可见时，虚拟化很有帮助。</span><span class="sxs-lookup"><span data-stu-id="0119b-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="0119b-108">Blazor 提供 [`Virtualize` 组件](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601)，可用于向应用的组件添加虚拟化。</span><span class="sxs-lookup"><span data-stu-id="0119b-108">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="0119b-109">在以下情况下，可使用 `Virtualize` 组件：</span><span class="sxs-lookup"><span data-stu-id="0119b-109">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="0119b-110">在循环中呈现一组数据项。</span><span class="sxs-lookup"><span data-stu-id="0119b-110">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="0119b-111">由于滚动，大多数项不可见。</span><span class="sxs-lookup"><span data-stu-id="0119b-111">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="0119b-112">呈现的项的大小完全相同。</span><span class="sxs-lookup"><span data-stu-id="0119b-112">The rendered items are exactly the same size.</span></span> <span data-ttu-id="0119b-113">当用户滚动到任意点时，组件可计算要显示的可见项。</span><span class="sxs-lookup"><span data-stu-id="0119b-113">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="0119b-114">如果不使用虚拟化，典型列表可能会使用 C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环来呈现列表中的每一项：</span><span class="sxs-lookup"><span data-stu-id="0119b-114">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="0119b-115">如果列表包含数千项，则呈现该列表可能会花费较长时间。</span><span class="sxs-lookup"><span data-stu-id="0119b-115">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="0119b-116">用户可能会遇到明显的 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="0119b-116">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="0119b-117">与其一次性呈现列表中的所有项，不如将 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环替换为 `Virtualize` 组件，并使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType> 指定固定的项源。</span><span class="sxs-lookup"><span data-stu-id="0119b-117">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0119b-118">这样，将仅呈现当前可见的项：</span><span class="sxs-lookup"><span data-stu-id="0119b-118">Only the items that are currently visible are rendered:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="0119b-119">如果未使用 `Context` 指定组件的上下文，请在项目内容模板中使用 `context` 值：</span><span class="sxs-lookup"><span data-stu-id="0119b-119">If not specifying a context to the component with `Context`, use the `context` value in the item content template:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="0119b-120">可通过 [`@key`](xref:mvc/views/razor#key) 指令属性来控制模型对象到元素和组件的映射过程。</span><span class="sxs-lookup"><span data-stu-id="0119b-120">The mapping process of model objects to elements and components can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="0119b-121">`@key` 使比较算法保证基于键的值保留元素或组件。</span><span class="sxs-lookup"><span data-stu-id="0119b-121">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="0119b-122">有关详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="0119b-122">For more information, see the following articles:</span></span>
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * <xref:mvc/views/razor#key>

<span data-ttu-id="0119b-123">`Virtualize` 组件：</span><span class="sxs-lookup"><span data-stu-id="0119b-123">The `Virtualize` component:</span></span>

* <span data-ttu-id="0119b-124">根据容器的高度和呈现的项的大小来计算要呈现的项数。</span><span class="sxs-lookup"><span data-stu-id="0119b-124">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="0119b-125">在用户滚动时，重新计算并重新呈现项。</span><span class="sxs-lookup"><span data-stu-id="0119b-125">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="0119b-126">仅从与当前可见区域相对应的外部 API 中获取记录切片，而不是下载集合中的所有数据。</span><span class="sxs-lookup"><span data-stu-id="0119b-126">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="0119b-127">`Virtualize` 组件的项内容可以包括：</span><span class="sxs-lookup"><span data-stu-id="0119b-127">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="0119b-128">纯 HTML 和 Razor 代码，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="0119b-128">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="0119b-129">一个或多个 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="0119b-129">One or more Razor components.</span></span>
* <span data-ttu-id="0119b-130">HTML/Razor 和 Razor 组件的组合。</span><span class="sxs-lookup"><span data-stu-id="0119b-130">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="0119b-131">项提供程序委托</span><span class="sxs-lookup"><span data-stu-id="0119b-131">Item provider delegate</span></span>

<span data-ttu-id="0119b-132">如果不想将所有项加载到内存中，可向组件的 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> 参数指定项提供程序委托方法，以按需异步检索请求的项。</span><span class="sxs-lookup"><span data-stu-id="0119b-132">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="0119b-133">在下面的示例中，`LoadEmployees` 方法向 `Virtualize` 组件提供项：</span><span class="sxs-lookup"><span data-stu-id="0119b-133">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="0119b-134">项提供程序接收 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>，它指定从特定起始索引开始的请求项数。</span><span class="sxs-lookup"><span data-stu-id="0119b-134">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="0119b-135">然后，项提供程序在数据库或其他服务中检索请求的项，并以 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> 形式将这些项与项总数一起返回。</span><span class="sxs-lookup"><span data-stu-id="0119b-135">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="0119b-136">项提供程序可以选择按每个请求检索项，也可以将项缓存以便后续使用。</span><span class="sxs-lookup"><span data-stu-id="0119b-136">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="0119b-137">`Virtualize` 组件只能从其参数中接受一个项源，因此，请不要尝试在使用项提供程序的同时为 `Items` 分配集合。</span><span class="sxs-lookup"><span data-stu-id="0119b-137">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="0119b-138">如果同时分配两个，则在运行时设置组件的参数时，将引发 <xref:System.InvalidOperationException>。</span><span class="sxs-lookup"><span data-stu-id="0119b-138">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="0119b-139">以下 `LoadEmployees` 方法示例从 `EmployeeService` 加载员工（未显示）：</span><span class="sxs-lookup"><span data-stu-id="0119b-139">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

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

<span data-ttu-id="0119b-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> 指示组件从其 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A> 重新请求数据。</span><span class="sxs-lookup"><span data-stu-id="0119b-140"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="0119b-141">当外部数据更改时，这很有用。</span><span class="sxs-lookup"><span data-stu-id="0119b-141">This is useful when external data changes.</span></span> <span data-ttu-id="0119b-142">使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> 时，无需对其进行调用。</span><span class="sxs-lookup"><span data-stu-id="0119b-142">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="0119b-143">占位符</span><span class="sxs-lookup"><span data-stu-id="0119b-143">Placeholder</span></span>

<span data-ttu-id="0119b-144">由于从远程数据源请求项可能需要一些时间，你可选择呈现包含项内容的占位符：</span><span class="sxs-lookup"><span data-stu-id="0119b-144">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="0119b-145">使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) 显示内容，直到项数据可用。</span><span class="sxs-lookup"><span data-stu-id="0119b-145">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="0119b-146">使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> 设置列表的项模板。</span><span class="sxs-lookup"><span data-stu-id="0119b-146">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

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

## <a name="item-size"></a><span data-ttu-id="0119b-147">项大小</span><span class="sxs-lookup"><span data-stu-id="0119b-147">Item size</span></span>

<span data-ttu-id="0119b-148">你可以使用 <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> 设置每个项的像素大小（默认值：50）：</span><span class="sxs-lookup"><span data-stu-id="0119b-148">The size of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="0119b-149">溢出扫描计数</span><span class="sxs-lookup"><span data-stu-id="0119b-149">Overscan count</span></span>

<span data-ttu-id="0119b-150"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> 确定在可见区域之前和之后呈现的额外项数。</span><span class="sxs-lookup"><span data-stu-id="0119b-150"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="0119b-151">此设置有助于降低滚动期间的呈现频率。</span><span class="sxs-lookup"><span data-stu-id="0119b-151">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="0119b-152">但是，值越大，页面中呈现的元素越多（默认值：3）：</span><span class="sxs-lookup"><span data-stu-id="0119b-152">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="0119b-153">状态更改</span><span class="sxs-lookup"><span data-stu-id="0119b-153">State changes</span></span>

<span data-ttu-id="0119b-154">当对 `Virtualize` 组件呈现的项进行更改时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以强制重新计算和重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="0119b-154">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
