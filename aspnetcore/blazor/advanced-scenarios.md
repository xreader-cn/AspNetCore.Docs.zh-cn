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
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="c399c-103">ASP.NET Core Blazor 高级方案</span><span class="sxs-lookup"><span data-stu-id="c399c-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="c399c-104">作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c399c-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="c399c-105">手动 RenderTreeBuilder 逻辑</span><span class="sxs-lookup"><span data-stu-id="c399c-105">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="c399c-106">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` 提供了用于操作组件和元素的方法，包括在代码C#中手动生成组件。</span><span class="sxs-lookup"><span data-stu-id="c399c-106">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="c399c-107">使用 `RenderTreeBuilder` 创建组件是一种高级方案。</span><span class="sxs-lookup"><span data-stu-id="c399c-107">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="c399c-108">格式不正确的组件（例如，未闭合的标记标记）可能会导致未定义的行为。</span><span class="sxs-lookup"><span data-stu-id="c399c-108">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="c399c-109">请考虑以下 `PetDetails` 组件，该组件可以手动内置到另一个组件中：</span><span class="sxs-lookup"><span data-stu-id="c399c-109">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="c399c-110">在下面的示例中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。</span><span class="sxs-lookup"><span data-stu-id="c399c-110">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="c399c-111">当调用 `RenderTreeBuilder` 方法来创建组件（`OpenComponent` 和 `AddAttribute`）时，序列号为源代码行号。</span><span class="sxs-lookup"><span data-stu-id="c399c-111">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="c399c-112">Blazor 差异算法依赖于与不同代码行对应的序列号，而不是不同的调用调用。</span><span class="sxs-lookup"><span data-stu-id="c399c-112">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="c399c-113">使用 `RenderTreeBuilder` 方法创建组件时，将序列号的参数硬编码。</span><span class="sxs-lookup"><span data-stu-id="c399c-113">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="c399c-114">**使用计算或计数器生成序列号会导致性能不佳。**</span><span class="sxs-lookup"><span data-stu-id="c399c-114">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="c399c-115">有关详细信息，请参阅 "[序列号与代码行号和非执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)" 部分。</span><span class="sxs-lookup"><span data-stu-id="c399c-115">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="c399c-116">`BuiltContent` 组件：</span><span class="sxs-lookup"><span data-stu-id="c399c-116">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="c399c-117">`Microsoft.AspNetCore.Components.RenderTree` 中的类型允许处理呈现操作的*结果*。</span><span class="sxs-lookup"><span data-stu-id="c399c-117">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="c399c-118">这是 Blazor 框架实现的内部详细信息。</span><span class="sxs-lookup"><span data-stu-id="c399c-118">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="c399c-119">这些类型应被视为不*稳定*，并且在将来的版本中可能会更改。</span><span class="sxs-lookup"><span data-stu-id="c399c-119">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="c399c-120">序列号与代码行号相关，而不是与执行顺序相关</span><span class="sxs-lookup"><span data-stu-id="c399c-120">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="c399c-121">Razor 组件文件（*razor*）始终被编译。</span><span class="sxs-lookup"><span data-stu-id="c399c-121">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="c399c-122">由于编译步骤可用于注入在运行时提高应用程序性能的信息，因此编译可能会与解释代码的优点相同。</span><span class="sxs-lookup"><span data-stu-id="c399c-122">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="c399c-123">这些改进的一个关键示例涉及*序列号*。</span><span class="sxs-lookup"><span data-stu-id="c399c-123">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="c399c-124">序列号指示运行时输出来自代码的不同和有序行。</span><span class="sxs-lookup"><span data-stu-id="c399c-124">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="c399c-125">运行时使用此信息以线性时间生成有效的树差异，其速度远快于一般树差异算法通常可以实现的速度。</span><span class="sxs-lookup"><span data-stu-id="c399c-125">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="c399c-126">请考虑以下 Razor 组件（*razor*）文件：</span><span class="sxs-lookup"><span data-stu-id="c399c-126">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="c399c-127">前面的代码编译为类似于下面的内容：</span><span class="sxs-lookup"><span data-stu-id="c399c-127">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="c399c-128">当第一次执行代码时，如果 `true``someFlag`，生成器将接收：</span><span class="sxs-lookup"><span data-stu-id="c399c-128">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="c399c-129">序列</span><span class="sxs-lookup"><span data-stu-id="c399c-129">Sequence</span></span> | <span data-ttu-id="c399c-130">类型</span><span class="sxs-lookup"><span data-stu-id="c399c-130">Type</span></span>      | <span data-ttu-id="c399c-131">数据</span><span class="sxs-lookup"><span data-stu-id="c399c-131">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c399c-132">0</span><span class="sxs-lookup"><span data-stu-id="c399c-132">0</span></span>        | <span data-ttu-id="c399c-133">Text 节点</span><span class="sxs-lookup"><span data-stu-id="c399c-133">Text node</span></span> | <span data-ttu-id="c399c-134">First</span><span class="sxs-lookup"><span data-stu-id="c399c-134">First</span></span>  |
| <span data-ttu-id="c399c-135">1</span><span class="sxs-lookup"><span data-stu-id="c399c-135">1</span></span>        | <span data-ttu-id="c399c-136">Text 节点</span><span class="sxs-lookup"><span data-stu-id="c399c-136">Text node</span></span> | <span data-ttu-id="c399c-137">秒</span><span class="sxs-lookup"><span data-stu-id="c399c-137">Second</span></span> |

<span data-ttu-id="c399c-138">假设 `someFlag` 成为 `false`，并再次呈现标记。</span><span class="sxs-lookup"><span data-stu-id="c399c-138">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="c399c-139">这次，生成器将接收：</span><span class="sxs-lookup"><span data-stu-id="c399c-139">This time, the builder receives:</span></span>

| <span data-ttu-id="c399c-140">序列</span><span class="sxs-lookup"><span data-stu-id="c399c-140">Sequence</span></span> | <span data-ttu-id="c399c-141">类型</span><span class="sxs-lookup"><span data-stu-id="c399c-141">Type</span></span>       | <span data-ttu-id="c399c-142">数据</span><span class="sxs-lookup"><span data-stu-id="c399c-142">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="c399c-143">1</span><span class="sxs-lookup"><span data-stu-id="c399c-143">1</span></span>        | <span data-ttu-id="c399c-144">Text 节点</span><span class="sxs-lookup"><span data-stu-id="c399c-144">Text node</span></span>  | <span data-ttu-id="c399c-145">秒</span><span class="sxs-lookup"><span data-stu-id="c399c-145">Second</span></span> |

<span data-ttu-id="c399c-146">当运行时执行差异时，它会发现序列 `0` 上的项已被删除，因此它生成以下简单的*编辑脚本*：</span><span class="sxs-lookup"><span data-stu-id="c399c-146">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="c399c-147">删除第一个文本节点。</span><span class="sxs-lookup"><span data-stu-id="c399c-147">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="c399c-148">以编程方式生成序列号的问题</span><span class="sxs-lookup"><span data-stu-id="c399c-148">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="c399c-149">假设你编写了以下呈现树生成器逻辑：</span><span class="sxs-lookup"><span data-stu-id="c399c-149">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="c399c-150">现在，第一个输出是：</span><span class="sxs-lookup"><span data-stu-id="c399c-150">Now, the first output is:</span></span>

| <span data-ttu-id="c399c-151">序列</span><span class="sxs-lookup"><span data-stu-id="c399c-151">Sequence</span></span> | <span data-ttu-id="c399c-152">类型</span><span class="sxs-lookup"><span data-stu-id="c399c-152">Type</span></span>      | <span data-ttu-id="c399c-153">数据</span><span class="sxs-lookup"><span data-stu-id="c399c-153">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c399c-154">0</span><span class="sxs-lookup"><span data-stu-id="c399c-154">0</span></span>        | <span data-ttu-id="c399c-155">Text 节点</span><span class="sxs-lookup"><span data-stu-id="c399c-155">Text node</span></span> | <span data-ttu-id="c399c-156">First</span><span class="sxs-lookup"><span data-stu-id="c399c-156">First</span></span>  |
| <span data-ttu-id="c399c-157">1</span><span class="sxs-lookup"><span data-stu-id="c399c-157">1</span></span>        | <span data-ttu-id="c399c-158">Text 节点</span><span class="sxs-lookup"><span data-stu-id="c399c-158">Text node</span></span> | <span data-ttu-id="c399c-159">秒</span><span class="sxs-lookup"><span data-stu-id="c399c-159">Second</span></span> |

<span data-ttu-id="c399c-160">此结果与以前的情况相同，因此不存在负面问题。</span><span class="sxs-lookup"><span data-stu-id="c399c-160">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="c399c-161">第二个呈现 `false` `someFlag`，输出为：</span><span class="sxs-lookup"><span data-stu-id="c399c-161">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="c399c-162">序列</span><span class="sxs-lookup"><span data-stu-id="c399c-162">Sequence</span></span> | <span data-ttu-id="c399c-163">类型</span><span class="sxs-lookup"><span data-stu-id="c399c-163">Type</span></span>      | <span data-ttu-id="c399c-164">数据</span><span class="sxs-lookup"><span data-stu-id="c399c-164">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="c399c-165">0</span><span class="sxs-lookup"><span data-stu-id="c399c-165">0</span></span>        | <span data-ttu-id="c399c-166">Text 节点</span><span class="sxs-lookup"><span data-stu-id="c399c-166">Text node</span></span> | <span data-ttu-id="c399c-167">秒</span><span class="sxs-lookup"><span data-stu-id="c399c-167">Second</span></span> |

<span data-ttu-id="c399c-168">这次，比较算法会发现*两个*更改已发生，并且算法将生成以下编辑脚本：</span><span class="sxs-lookup"><span data-stu-id="c399c-168">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="c399c-169">将第一个文本节点的值更改为 `Second`。</span><span class="sxs-lookup"><span data-stu-id="c399c-169">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="c399c-170">删除第二个文本节点。</span><span class="sxs-lookup"><span data-stu-id="c399c-170">Remove the second text node.</span></span>

<span data-ttu-id="c399c-171">生成序列号会丢失有关原始代码中 `if/else` 分支和循环的位置的所有有用信息。</span><span class="sxs-lookup"><span data-stu-id="c399c-171">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="c399c-172">这会导致**两次**比较，就像以前一样。</span><span class="sxs-lookup"><span data-stu-id="c399c-172">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="c399c-173">这是一个简单的示例。</span><span class="sxs-lookup"><span data-stu-id="c399c-173">This is a trivial example.</span></span> <span data-ttu-id="c399c-174">在具有复杂、深度嵌套结构的更真实的情况下，特别是在循环中，性能开销通常较高。</span><span class="sxs-lookup"><span data-stu-id="c399c-174">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="c399c-175">比较算法不必立即确定哪些循环块或分支已插入或删除，而是必须将其深度递归到呈现树中。</span><span class="sxs-lookup"><span data-stu-id="c399c-175">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="c399c-176">这通常会导致生成更长的编辑脚本，因为比较算法 misinformed 了旧结构与新结构之间的关系。</span><span class="sxs-lookup"><span data-stu-id="c399c-176">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="c399c-177">指导和结论</span><span class="sxs-lookup"><span data-stu-id="c399c-177">Guidance and conclusions</span></span>

* <span data-ttu-id="c399c-178">如果动态生成了序列号，则应用程序性能会受到影响。</span><span class="sxs-lookup"><span data-stu-id="c399c-178">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="c399c-179">框架无法在运行时自动创建自己的序列号，因为所需信息不存在，除非它在编译时捕获。</span><span class="sxs-lookup"><span data-stu-id="c399c-179">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="c399c-180">请勿写入长块手动实现 `RenderTreeBuilder` 逻辑。</span><span class="sxs-lookup"><span data-stu-id="c399c-180">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="c399c-181">首选*razor*文件，并允许编译器处理序列号。</span><span class="sxs-lookup"><span data-stu-id="c399c-181">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="c399c-182">如果无法避免手动 `RenderTreeBuilder` 逻辑，请将长代码块拆分为 `OpenRegion`/`CloseRegion` 调用中的小块。</span><span class="sxs-lookup"><span data-stu-id="c399c-182">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="c399c-183">每个区域都有其自己单独的序列号空间，因此可以从每个区域内的零（或任何其他任意数字）重新启动。</span><span class="sxs-lookup"><span data-stu-id="c399c-183">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="c399c-184">如果对序列号进行硬编码，则 diff 算法只要求序列号的值增加。</span><span class="sxs-lookup"><span data-stu-id="c399c-184">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="c399c-185">初始值和间隙无关。</span><span class="sxs-lookup"><span data-stu-id="c399c-185">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="c399c-186">一个合法选项是将代码行号用作序列号，或从零开始，并按一个或数百个（或任何首选间隔）递增。</span><span class="sxs-lookup"><span data-stu-id="c399c-186">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="c399c-187"> 使用序列号，而其他树比较的 UI 框架不使用序列号。</span><span class="sxs-lookup"><span data-stu-id="c399c-187"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="c399c-188">使用序列号时，比较速度要快得多，Blazor 具有一个编译步骤，该步骤会自动处理序列号，以便为开发人员创作*razor*文件。</span><span class="sxs-lookup"><span data-stu-id="c399c-188">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>
