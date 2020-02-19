---
title: ASP.NET Core Blazor 高级方案
author: guardrex
description: 了解 Blazor中的高级方案，包括如何在应用中合并手动 RenderTreeBuilder 逻辑。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5e0618faa7b1b5e4cc15e30d9c16afaf7ccabaf0
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453180"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET Core Blazor 高级方案

作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)

## <a name="manual-rendertreebuilder-logic"></a>手动 RenderTreeBuilder 逻辑

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` 提供了用于操作组件和元素的方法，包括在代码C#中手动生成组件。

> [!NOTE]
> 使用 `RenderTreeBuilder` 创建组件是一种高级方案。 格式不正确的组件（例如，未闭合的标记标记）可能会导致未定义的行为。

请考虑以下 `PetDetails` 组件，该组件可以手动内置到另一个组件中：

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

在下面的示例中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。 当调用 `RenderTreeBuilder` 方法来创建组件（`OpenComponent` 和 `AddAttribute`）时，序列号为源代码行号。 Blazor 差异算法依赖于与不同代码行对应的序列号，而不是不同的调用调用。 使用 `RenderTreeBuilder` 方法创建组件时，将序列号的参数硬编码。 **使用计算或计数器生成序列号会导致性能不佳。** 有关详细信息，请参阅 "[序列号与代码行号和非执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)" 部分。

`BuiltContent` 组件：

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> `Microsoft.AspNetCore.Components.RenderTree` 中的类型允许处理呈现操作的*结果*。 这是 Blazor 框架实现的内部详细信息。 这些类型应被视为不*稳定*，并且在将来的版本中可能会更改。

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>序列号与代码行号相关，而不是与执行顺序相关

Razor 组件文件（*razor*）始终被编译。 由于编译步骤可用于注入在运行时提高应用程序性能的信息，因此编译可能会与解释代码的优点相同。

这些改进的一个关键示例涉及*序列号*。 序列号指示运行时输出来自代码的不同和有序行。 运行时使用此信息以线性时间生成有效的树差异，其速度远快于一般树差异算法通常可以实现的速度。

请考虑以下 Razor 组件（*razor*）文件：

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

前面的代码编译为类似于下面的内容：

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

当第一次执行代码时，如果 `true``someFlag`，生成器将接收：

| 序列 | 类型      | 数据   |
| :------: | --------- | :----: |
| 0        | Text 节点 | First  |
| 1        | Text 节点 | 秒 |

假设 `someFlag` 成为 `false`，并再次呈现标记。 这次，生成器将接收：

| 序列 | 类型       | 数据   |
| :------: | ---------- | :----: |
| 1        | Text 节点  | 秒 |

当运行时执行差异时，它会发现序列 `0` 上的项已被删除，因此它生成以下简单的*编辑脚本*：

* 删除第一个文本节点。

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>以编程方式生成序列号的问题

假设你编写了以下呈现树生成器逻辑：

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

现在，第一个输出是：

| 序列 | 类型      | 数据   |
| :------: | --------- | :----: |
| 0        | Text 节点 | First  |
| 1        | Text 节点 | 秒 |

此结果与以前的情况相同，因此不存在负面问题。 第二个呈现 `false` `someFlag`，输出为：

| 序列 | 类型      | 数据   |
| :------: | --------- | ------ |
| 0        | Text 节点 | 秒 |

这次，比较算法会发现*两个*更改已发生，并且算法将生成以下编辑脚本：

* 将第一个文本节点的值更改为 `Second`。
* 删除第二个文本节点。

生成序列号会丢失有关原始代码中 `if/else` 分支和循环的位置的所有有用信息。 这会导致**两次**比较，就像以前一样。

这是一个简单的示例。 在具有复杂、深度嵌套结构的更真实的情况下，特别是在循环中，性能开销通常较高。 比较算法不必立即确定哪些循环块或分支已插入或删除，而是必须将其深度递归到呈现树中。 这通常会导致生成更长的编辑脚本，因为比较算法 misinformed 了旧结构与新结构之间的关系。

### <a name="guidance-and-conclusions"></a>指导和结论

* 如果动态生成了序列号，则应用程序性能会受到影响。
* 框架无法在运行时自动创建自己的序列号，因为所需信息不存在，除非它在编译时捕获。
* 请勿写入长块手动实现 `RenderTreeBuilder` 逻辑。 首选*razor*文件，并允许编译器处理序列号。 如果无法避免手动 `RenderTreeBuilder` 逻辑，请将长代码块拆分为 `OpenRegion`/`CloseRegion` 调用中的小块。 每个区域都有其自己单独的序列号空间，因此可以从每个区域内的零（或任何其他任意数字）重新启动。
* 如果对序列号进行硬编码，则 diff 算法只要求序列号的值增加。 初始值和间隙无关。 一个合法选项是将代码行号用作序列号，或从零开始，并按一个或数百个（或任何首选间隔）递增。 
* Blazor 使用序列号，而其他树比较的 UI 框架不使用序列号。 使用序列号时，比较速度要快得多，Blazor 具有一个编译步骤，该步骤会自动处理序列号，以便为开发人员创作*razor*文件。
