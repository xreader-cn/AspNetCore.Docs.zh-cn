---
title: ASP.NET Core Blazor 模板组件
author: guardrex
description: 了解模板化组件如何接受一个或多个 UI 模板作为参数，然后可以将这些模板用作组件呈现逻辑的一部分。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b64d6a731e540b13c50b2c6108f75efd0ac9290c
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453150"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a>ASP.NET Core Blazor 模板组件

作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)

模板化组件是接受一个或多个 UI 模板作为参数的组件，可将其用作组件呈现逻辑的一部分。 模板化组件允许你创作比常规组件更易于使用的更高级别的组件。 几个示例包括：

* 允许用户为表的标头、行和脚注指定模板的表组件。
* 允许用户在列表中指定用于呈现项的模板的列表组件。

## <a name="template-parameters"></a>模板参数

模板化组件通过指定 `RenderFragment` 或 `RenderFragment<T>`类型的一个或多个组件参数进行定义。 呈现片段表示要呈现的 UI 段。 `RenderFragment<T>` 采用可在调用呈现片段时指定的类型参数。

`TableTemplate` 组件：

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

使用模板化组件时，可以使用与参数名称匹配的子元素（在以下示例中为`TableHeader` 和 `RowTemplate`）指定模板参数：

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
```

## <a name="template-context-parameters"></a>模板上下文参数

作为元素传递的类型 `RenderFragment<T>` 的组件参数具有一个名为 `context` 的隐式参数（例如，前面的代码示例 `@context.PetId`），但你可以使用子元素上的 `Context` 特性来更改参数名称。 在下面的示例中，`RowTemplate` 元素的 `Context` 特性指定了 `pet` 参数：

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
```

或者，您可以在 component 元素上指定 `Context` 特性。 指定的 `Context` 特性适用于所有指定的模板参数。 如果要为隐式子内容指定内容参数名称（不包含任何换行子元素），这会很有用。 在下面的示例中，`Context` 特性显示在 `TableTemplate` 元素上，并应用于所有模板参数：

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
```

## <a name="generic-typed-components"></a>泛型类型化组件

模板化组件通常是通用类型。 例如，泛型 `ListViewTemplate` 组件可用于呈现 `IEnumerable<T>` 值。 若要定义一般组件，请使用[`@typeparam`](xref:mvc/views/razor#typeparam)指令指定类型参数：

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

当使用泛型类型的组件时，将在可能的情况下推断类型参数：

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

否则，必须使用与类型参数的名称匹配的属性显式指定 type 参数。 在下面的示例中，`TItem="Pet"` 指定类型：

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
