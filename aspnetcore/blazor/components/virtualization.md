---
title: ASP.NET Core Blazor 组件虚拟化
author: guardrex
description: 了解如何在 ASP.NET Core Blazor 应用中使用组件虚拟化。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2020
no-loc:
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
ms.openlocfilehash: 911eeeb445741aa1519e1464dd4a75e26f6f12ab
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847567"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="81e15-103">ASP.NET Core Blazor 组件虚拟化</span><span class="sxs-lookup"><span data-stu-id="81e15-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="81e15-104">作者：[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="81e15-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="81e15-105">使用 Blazor 框架的内置虚拟化支持提高组件呈现的感知性能。</span><span class="sxs-lookup"><span data-stu-id="81e15-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="81e15-106">虚拟化是一种技术，用于将 UI 呈现限制为仅当前可见的部分。</span><span class="sxs-lookup"><span data-stu-id="81e15-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="81e15-107">例如，当应用必须呈现一个长列表或一个包含许多行的表，并且在任何给定的时间只需要一小部分项可见时，虚拟化很有帮助。</span><span class="sxs-lookup"><span data-stu-id="81e15-107">For example, virtualization is helpful when the app must render a long list or a table with many rows and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="81e15-108">Blazor 提供 `Virtualize` 组件，可用于向应用的组件添加虚拟化。</span><span class="sxs-lookup"><span data-stu-id="81e15-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="81e15-109">如果不使用虚拟化，基于列表或表的典型组件可能会使用 C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环来呈现列表中的每一项或表中的每一行：</span><span class="sxs-lookup"><span data-stu-id="81e15-109">Without virtualization, a typical list or table-based component might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list or each row in the table:</span></span>

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

<span data-ttu-id="81e15-110">如果列表包含数千项，则呈现该列表可能会花费较长时间。</span><span class="sxs-lookup"><span data-stu-id="81e15-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="81e15-111">用户可能会遇到明显的 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="81e15-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="81e15-112">与其一次性呈现列表中的所有项，不如将 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 循环替换为 `Virtualize` 组件，并使用 `Items` 指定固定的项源。</span><span class="sxs-lookup"><span data-stu-id="81e15-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="81e15-113">这样，将仅呈现当前可见的项：</span><span class="sxs-lookup"><span data-stu-id="81e15-113">Only the items that are currently visible are rendered:</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="81e15-114">如果未使用 `Context` 指定组件的上下文，请在项目内容模板中使用 `context` 值 (`@context.{PROPERTY}`)：</span><span class="sxs-lookup"><span data-stu-id="81e15-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="81e15-115">`Virtualize` 组件根据容器的高度和呈现的项的大小来计算要呈现的项数。</span><span class="sxs-lookup"><span data-stu-id="81e15-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="81e15-116">项提供程序委托</span><span class="sxs-lookup"><span data-stu-id="81e15-116">Item provider delegate</span></span>

<span data-ttu-id="81e15-117">如果不想将所有项加载到内存中，可向组件的 `ItemsProvider` 参数指定项提供程序委托方法，以按需异步检索请求的项：</span><span class="sxs-lookup"><span data-stu-id="81e15-117">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="81e15-118">项提供程序接收 `ItemsProviderRequest`，它指定从特定起始索引开始的请求项数。</span><span class="sxs-lookup"><span data-stu-id="81e15-118">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="81e15-119">然后，项提供程序在数据库或其他服务中检索请求的项，并以 `ItemsProviderResult<TItem>` 形式将这些项与项总数一起返回。</span><span class="sxs-lookup"><span data-stu-id="81e15-119">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="81e15-120">项提供程序可以选择按每个请求检索项，也可以将项缓存以便后续使用。</span><span class="sxs-lookup"><span data-stu-id="81e15-120">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="81e15-121">请勿尝试使用项提供程序来为同一个 `Virtualize` 组件分配 `Items` 的集合。</span><span class="sxs-lookup"><span data-stu-id="81e15-121">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="81e15-122">以下示例从 `EmployeeService` 加载员工：</span><span class="sxs-lookup"><span data-stu-id="81e15-122">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="81e15-123">占位符</span><span class="sxs-lookup"><span data-stu-id="81e15-123">Placeholder</span></span>

<span data-ttu-id="81e15-124">由于从远程数据源请求项可能需要一些时间，你可以选择呈现占位符 (`<Placeholder>...</Placeholder>`)，直到项数据可用：</span><span class="sxs-lookup"><span data-stu-id="81e15-124">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a><span data-ttu-id="81e15-125">项大小</span><span class="sxs-lookup"><span data-stu-id="81e15-125">Item size</span></span>

<span data-ttu-id="81e15-126">你可以使用 `ItemSize` 设置每个项的像素大小（默认值：50px）：</span><span class="sxs-lookup"><span data-stu-id="81e15-126">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a><span data-ttu-id="81e15-127">溢出扫描计数</span><span class="sxs-lookup"><span data-stu-id="81e15-127">Overscan count</span></span>

<span data-ttu-id="81e15-128">`OverscanCount` 确定在可见区域之前和之后呈现的额外项数。</span><span class="sxs-lookup"><span data-stu-id="81e15-128">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="81e15-129">此设置有助于降低滚动期间的呈现频率。</span><span class="sxs-lookup"><span data-stu-id="81e15-129">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="81e15-130">但是，值越大，页面中呈现的元素越多（默认值：3）：</span><span class="sxs-lookup"><span data-stu-id="81e15-130">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="81e15-131">例如，如果某网格或列表要呈现数百个包含组件的行，则该网格或列表呈现时会大量使用处理器。</span><span class="sxs-lookup"><span data-stu-id="81e15-131">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="81e15-132">请考虑将网格或列表布局虚拟化，以便在任何给定时间都只呈现其中的一部分组件。</span><span class="sxs-lookup"><span data-stu-id="81e15-132">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="81e15-133">有关组件子集呈现的示例，请参阅 [`Virtualization` 示例应用（aspnet/示例 GitHub 存储库）中的以下组件](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)：</span><span class="sxs-lookup"><span data-stu-id="81e15-133">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="81e15-134">`Virtualize` 组件 ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs))：用 C# 语言编写的一种组件，实现了 <xref:Microsoft.AspNetCore.Components.ComponentBase> 来根据用户滚动呈现一组天气数据行。</span><span class="sxs-lookup"><span data-stu-id="81e15-134">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="81e15-135">`FetchData` 组件 ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor))：使用 `Virtualize` 组件一次显示 25 行天气数据。</span><span class="sxs-lookup"><span data-stu-id="81e15-135">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="81e15-136">状态更改</span><span class="sxs-lookup"><span data-stu-id="81e15-136">State changes</span></span>

<span data-ttu-id="81e15-137">当对 `Virtualize` 组件呈现的项进行更改时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 以强制重新计算和重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="81e15-137">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
