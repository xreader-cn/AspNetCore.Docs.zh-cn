---
title: ASP.NET Core [Blazor 高级方案
author: guardrex
description: 了解 [Blazor 中的高级方案，包括如何将 RenderTreeBuilder 手动逻辑合并到应用中。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: blazor/advanced-scenarios
ms.openlocfilehash: bdea9f2fe5c552b56414bb49588733c8dc2a34db
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400214"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="6519b-103">ASP.NET Core [Blazor 高级方案</span><span class="sxs-lookup"><span data-stu-id="6519b-103">ASP.NET Core [Blazor advanced scenarios</span></span>

<span data-ttu-id="6519b-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6519b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="6519b-105">[Blazor Server 线路处理程序</span><span class="sxs-lookup"><span data-stu-id="6519b-105">[Blazor Server circuit handler</span></span>

<span data-ttu-id="6519b-106">[Blazor Server 允许代码定义线路处理程序，后者允许在用户线路的状态发生更改时运行代码。</span><span class="sxs-lookup"><span data-stu-id="6519b-106">[Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="6519b-107">线路处理程序通过从 `CircuitHandler` 派生并在应用的服务容器中注册该类实现。</span><span class="sxs-lookup"><span data-stu-id="6519b-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="6519b-108">以下线路处理程序示例跟踪打开的 [SignalR 连接：</span><span class="sxs-lookup"><span data-stu-id="6519b-108">The following example of a circuit handler tracks open [SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="6519b-109">线路处理程序使用 DI 注册。</span><span class="sxs-lookup"><span data-stu-id="6519b-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="6519b-110">每个线路实例都会创建区分范围的实例。</span><span class="sxs-lookup"><span data-stu-id="6519b-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="6519b-111">借助前面示例中的 `TrackingCircuitHandler` 创建单一实例服务，因为必须跟踪所有线路的状态：</span><span class="sxs-lookup"><span data-stu-id="6519b-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="6519b-112">如果自定义线路处理程序的方法引发未处理异常，则该异常会导致 [Blazor Server 线路产生严重错误。</span><span class="sxs-lookup"><span data-stu-id="6519b-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the [Blazor Server circuit.</span></span> <span data-ttu-id="6519b-113">若要容忍处理程序代码或被调用方法中的异常，请使用错误处理和日志记录将代码包装到一个或多个 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句中。</span><span class="sxs-lookup"><span data-stu-id="6519b-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="6519b-114">当线路因用户断开连接而结束且框架正在清除线路状态时，框架会处置线路的 DI 范围。</span><span class="sxs-lookup"><span data-stu-id="6519b-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="6519b-115">处置范围时会处置所有实现 <xref:System.IDisposable?displayProperty=fullName> 的区分线路范围的 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="6519b-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="6519b-116">如果有任何 DI 服务在处置期间引发未处理异常，则框架会记录该异常。</span><span class="sxs-lookup"><span data-stu-id="6519b-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="6519b-117">RenderTreeBuilder 手动逻辑</span><span class="sxs-lookup"><span data-stu-id="6519b-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="6519b-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 提供用于操作组件和元素的方法，包括在 C# 代码中手动生成组件。</span><span class="sxs-lookup"><span data-stu-id="6519b-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="6519b-119">使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 创建组件是一种高级方案。</span><span class="sxs-lookup"><span data-stu-id="6519b-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="6519b-120">格式不正确的组件（例如，未封闭的标记标签）可能导致未定义的行为。</span><span class="sxs-lookup"><span data-stu-id="6519b-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="6519b-121">考虑以下 `PetDetails` 组件，可将其手动生成到另一个组件中：</span><span class="sxs-lookup"><span data-stu-id="6519b-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="6519b-122">在以下示例中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。</span><span class="sxs-lookup"><span data-stu-id="6519b-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="6519b-123">调用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法创建组件（`OpenComponent` 和 `AddAttribute`）时，序列号为源代码行号。</span><span class="sxs-lookup"><span data-stu-id="6519b-123">When calling <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="6519b-124">[Blazor 差分算法依赖于对应于不同代码行（而不是不同调用的调用）的序列号。</span><span class="sxs-lookup"><span data-stu-id="6519b-124">The [Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="6519b-125">使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法创建组件时，请对序列号的参数进行硬编码。</span><span class="sxs-lookup"><span data-stu-id="6519b-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="6519b-126">**通过计算或计数器生成序列号可能导致性能不佳。**</span><span class="sxs-lookup"><span data-stu-id="6519b-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="6519b-127">有关详细信息，请参阅[序列号与代码行号相关，而不与执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)部分。</span><span class="sxs-lookup"><span data-stu-id="6519b-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="6519b-128">`BuiltContent` 组件：</span><span class="sxs-lookup"><span data-stu-id="6519b-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="6519b-129"><xref:Microsoft.AspNetCore.Components.RenderTree> 中的类型允许处理呈现操作的*结果*。</span><span class="sxs-lookup"><span data-stu-id="6519b-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="6519b-130">这些是 [Blazor 框架实现的内部细节。</span><span class="sxs-lookup"><span data-stu-id="6519b-130">These are internal details of the [Blazor framework implementation.</span></span> <span data-ttu-id="6519b-131">这些类型应视为*不稳定*，并且在未来版本中可能会有更改。</span><span class="sxs-lookup"><span data-stu-id="6519b-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="6519b-132">序列号与代码行号相关，而不与执行顺序相关</span><span class="sxs-lookup"><span data-stu-id="6519b-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="6519b-133">[Razor 组件文件 (`.razor`) 始终被编译。</span><span class="sxs-lookup"><span data-stu-id="6519b-133">[Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="6519b-134">与解释代码相比，编译具有潜在优势，因为编译步骤可用于注入信息，从而在运行时提高应用性能。</span><span class="sxs-lookup"><span data-stu-id="6519b-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="6519b-135">这些改进的关键示例涉及*序列号*。</span><span class="sxs-lookup"><span data-stu-id="6519b-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="6519b-136">序列号向运行时指示哪些输出来自哪些不同的已排序代码行。</span><span class="sxs-lookup"><span data-stu-id="6519b-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="6519b-137">运行时使用此信息在线性时间内生成高效的树上差分，这比常规树上差分算法通常可以做到的速度快得多。</span><span class="sxs-lookup"><span data-stu-id="6519b-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="6519b-138">请考虑使用以下 [Razor 组件（`.razor` 文件）：</span><span class="sxs-lookup"><span data-stu-id="6519b-138">Consider the following [Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="6519b-139">前面的代码编译为以下所示内容：</span><span class="sxs-lookup"><span data-stu-id="6519b-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="6519b-140">首次执行代码时，如果 `someFlag` 为 `true`，则生成器会收到：</span><span class="sxs-lookup"><span data-stu-id="6519b-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="6519b-141">序列</span><span class="sxs-lookup"><span data-stu-id="6519b-141">Sequence</span></span> | <span data-ttu-id="6519b-142">类型</span><span class="sxs-lookup"><span data-stu-id="6519b-142">Type</span></span>      | <span data-ttu-id="6519b-143">数据</span><span class="sxs-lookup"><span data-stu-id="6519b-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="6519b-144">0</span><span class="sxs-lookup"><span data-stu-id="6519b-144">0</span></span>        | <span data-ttu-id="6519b-145">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6519b-145">Text node</span></span> | <span data-ttu-id="6519b-146">First</span><span class="sxs-lookup"><span data-stu-id="6519b-146">First</span></span>  |
| <span data-ttu-id="6519b-147">1</span><span class="sxs-lookup"><span data-stu-id="6519b-147">1</span></span>        | <span data-ttu-id="6519b-148">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6519b-148">Text node</span></span> | <span data-ttu-id="6519b-149">秒</span><span class="sxs-lookup"><span data-stu-id="6519b-149">Second</span></span> |

<span data-ttu-id="6519b-150">假设 `someFlag` 变为 `false` 且标记再次呈现。</span><span class="sxs-lookup"><span data-stu-id="6519b-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="6519b-151">此时，生成器会收到：</span><span class="sxs-lookup"><span data-stu-id="6519b-151">This time, the builder receives:</span></span>

| <span data-ttu-id="6519b-152">序列</span><span class="sxs-lookup"><span data-stu-id="6519b-152">Sequence</span></span> | <span data-ttu-id="6519b-153">类型</span><span class="sxs-lookup"><span data-stu-id="6519b-153">Type</span></span>       | <span data-ttu-id="6519b-154">数据</span><span class="sxs-lookup"><span data-stu-id="6519b-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="6519b-155">1</span><span class="sxs-lookup"><span data-stu-id="6519b-155">1</span></span>        | <span data-ttu-id="6519b-156">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6519b-156">Text node</span></span>  | <span data-ttu-id="6519b-157">秒</span><span class="sxs-lookup"><span data-stu-id="6519b-157">Second</span></span> |

<span data-ttu-id="6519b-158">当运行时执行差分时，它会看到序列 `0` 处的项目已被删除，因此，它会生成以下普通*编辑脚本*：</span><span class="sxs-lookup"><span data-stu-id="6519b-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="6519b-159">删除第一个文本节点。</span><span class="sxs-lookup"><span data-stu-id="6519b-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="6519b-160">以编程方式生成序列号的问题</span><span class="sxs-lookup"><span data-stu-id="6519b-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="6519b-161">想象一下，你编写了以下呈现树生成器逻辑：</span><span class="sxs-lookup"><span data-stu-id="6519b-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="6519b-162">现在，第一个输出是：</span><span class="sxs-lookup"><span data-stu-id="6519b-162">Now, the first output is:</span></span>

| <span data-ttu-id="6519b-163">序列</span><span class="sxs-lookup"><span data-stu-id="6519b-163">Sequence</span></span> | <span data-ttu-id="6519b-164">类型</span><span class="sxs-lookup"><span data-stu-id="6519b-164">Type</span></span>      | <span data-ttu-id="6519b-165">数据</span><span class="sxs-lookup"><span data-stu-id="6519b-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="6519b-166">0</span><span class="sxs-lookup"><span data-stu-id="6519b-166">0</span></span>        | <span data-ttu-id="6519b-167">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6519b-167">Text node</span></span> | <span data-ttu-id="6519b-168">First</span><span class="sxs-lookup"><span data-stu-id="6519b-168">First</span></span>  |
| <span data-ttu-id="6519b-169">1</span><span class="sxs-lookup"><span data-stu-id="6519b-169">1</span></span>        | <span data-ttu-id="6519b-170">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6519b-170">Text node</span></span> | <span data-ttu-id="6519b-171">秒</span><span class="sxs-lookup"><span data-stu-id="6519b-171">Second</span></span> |

<span data-ttu-id="6519b-172">此结果与之前的示例相同，因此不存在负面问题。</span><span class="sxs-lookup"><span data-stu-id="6519b-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="6519b-173">在第二个呈现中，`someFlag` 为 `false`，输出为：</span><span class="sxs-lookup"><span data-stu-id="6519b-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="6519b-174">序列</span><span class="sxs-lookup"><span data-stu-id="6519b-174">Sequence</span></span> | <span data-ttu-id="6519b-175">类型</span><span class="sxs-lookup"><span data-stu-id="6519b-175">Type</span></span>      | <span data-ttu-id="6519b-176">数据</span><span class="sxs-lookup"><span data-stu-id="6519b-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="6519b-177">0</span><span class="sxs-lookup"><span data-stu-id="6519b-177">0</span></span>        | <span data-ttu-id="6519b-178">Text 节点</span><span class="sxs-lookup"><span data-stu-id="6519b-178">Text node</span></span> | <span data-ttu-id="6519b-179">秒</span><span class="sxs-lookup"><span data-stu-id="6519b-179">Second</span></span> |

<span data-ttu-id="6519b-180">此时，差分算法发现发生了*两个*变化，且算法生成以下编辑脚本：</span><span class="sxs-lookup"><span data-stu-id="6519b-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="6519b-181">将第一个文本节点的值更改为 `Second`。</span><span class="sxs-lookup"><span data-stu-id="6519b-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="6519b-182">删除第二个文本节点。</span><span class="sxs-lookup"><span data-stu-id="6519b-182">Remove the second text node.</span></span>

<span data-ttu-id="6519b-183">生成序列号会丢失有关原始代码中 `if/else` 分支和循环的位置的所有有用信息。</span><span class="sxs-lookup"><span data-stu-id="6519b-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="6519b-184">这会导致**两倍于**之前长度的差异。</span><span class="sxs-lookup"><span data-stu-id="6519b-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="6519b-185">这是一个普通示例。</span><span class="sxs-lookup"><span data-stu-id="6519b-185">This is a trivial example.</span></span> <span data-ttu-id="6519b-186">在具有深度嵌套的复杂结构（尤其是带有循环）的更真实的情况下，性能成本通常会更高。</span><span class="sxs-lookup"><span data-stu-id="6519b-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="6519b-187">差分算法必须深入递归到呈现树中，而不是立即确定已插入或删除的循环块或分支。</span><span class="sxs-lookup"><span data-stu-id="6519b-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="6519b-188">这通常导致必须生成更长的编辑脚本，因为差分算法获知了关于新旧结构之间关系的错误信息。</span><span class="sxs-lookup"><span data-stu-id="6519b-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="6519b-189">指南和结论</span><span class="sxs-lookup"><span data-stu-id="6519b-189">Guidance and conclusions</span></span>

* <span data-ttu-id="6519b-190">如果动态生成序列号，则应用性能会受到影响。</span><span class="sxs-lookup"><span data-stu-id="6519b-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="6519b-191">该框架无法在运行时自动创建自己的序列号，因为除非在编译时捕获了必需的信息，否则这些信息不存在。</span><span class="sxs-lookup"><span data-stu-id="6519b-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="6519b-192">不要编写手动实现的冗长 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 逻辑块。</span><span class="sxs-lookup"><span data-stu-id="6519b-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="6519b-193">优先使用 `.razor` 文件并允许编译器处理序列号。</span><span class="sxs-lookup"><span data-stu-id="6519b-193">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="6519b-194">如果无法避免 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 手动逻辑，请将较长的代码块拆分为封装在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 调用中的较小部分。</span><span class="sxs-lookup"><span data-stu-id="6519b-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="6519b-195">每个区域都有自己的独立序列号空间，因此可在每个区域内从零（或任何其他任意数）重新开始。</span><span class="sxs-lookup"><span data-stu-id="6519b-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="6519b-196">如果序列号已硬编码，则差分算法仅要求序列号的值增加。</span><span class="sxs-lookup"><span data-stu-id="6519b-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="6519b-197">初始值和间隔不相关。</span><span class="sxs-lookup"><span data-stu-id="6519b-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="6519b-198">一个合理选择是使用代码行号作为序列号，或者从零开始并以 1 或 100 的间隔（或任何首选间隔）增加。</span><span class="sxs-lookup"><span data-stu-id="6519b-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="6519b-199">[Blazor 使用序列号，而其他树上差分 UI 框架不使用它们。</span><span class="sxs-lookup"><span data-stu-id="6519b-199">[Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="6519b-200">使用序列号时，差分速度要快得多，并且 [Blazor 的优势在于编译步骤可为编写 `.razor` 文件的开发人员自动处理序列号。</span><span class="sxs-lookup"><span data-stu-id="6519b-200">Diffing is far faster when sequence numbers are used, and [Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="6519b-201">在 [Blazor Server 应用中执行大型数据传输</span><span class="sxs-lookup"><span data-stu-id="6519b-201">Perform large data transfers in [Blazor Server apps</span></span>

<span data-ttu-id="6519b-202">在某些方案中，必须在 JavaScript 和 [Blazor 之间传输大量数据。</span><span class="sxs-lookup"><span data-stu-id="6519b-202">In some scenarios, large amounts of data must be transferred between JavaScript and [Blazor.</span></span> <span data-ttu-id="6519b-203">通常，大型数据传输在以下情况中发生：</span><span class="sxs-lookup"><span data-stu-id="6519b-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="6519b-204">浏览器文件系统 API 用于上传或下载文件。</span><span class="sxs-lookup"><span data-stu-id="6519b-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="6519b-205">需要与第三方库互操作。</span><span class="sxs-lookup"><span data-stu-id="6519b-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="6519b-206">[Blazor Server 中存在限制，可防止传递可能导致性能问题的单个大型消息。</span><span class="sxs-lookup"><span data-stu-id="6519b-206">In [Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="6519b-207">开发在 JavaScript 和 [Blazor 之间传输数据的代码时，请考虑以下指南：</span><span class="sxs-lookup"><span data-stu-id="6519b-207">Consider the following guidance when developing code that transfers data between JavaScript and [Blazor:</span></span>

* <span data-ttu-id="6519b-208">将数据切成小块，然后按顺序发送数据段，直到服务器收到所有数据。</span><span class="sxs-lookup"><span data-stu-id="6519b-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="6519b-209">不要在 JavaScript 和 C# 代码中分配大型对象。</span><span class="sxs-lookup"><span data-stu-id="6519b-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="6519b-210">发送或接收数据时，请勿长时间阻止主 UI 线程。</span><span class="sxs-lookup"><span data-stu-id="6519b-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="6519b-211">在进程完成或取消时释放消耗的所有内存。</span><span class="sxs-lookup"><span data-stu-id="6519b-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="6519b-212">为了安全起见，请强制执行以下附加要求：</span><span class="sxs-lookup"><span data-stu-id="6519b-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="6519b-213">声明可以传递的最大文件或数据大小。</span><span class="sxs-lookup"><span data-stu-id="6519b-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="6519b-214">声明从客户端到服务器的最低上传速率。</span><span class="sxs-lookup"><span data-stu-id="6519b-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="6519b-215">在服务器收到数据后，数据可以：</span><span class="sxs-lookup"><span data-stu-id="6519b-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="6519b-216">暂时存储在内存缓冲区中，直到收集完所有数据段。</span><span class="sxs-lookup"><span data-stu-id="6519b-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="6519b-217">立即使用。</span><span class="sxs-lookup"><span data-stu-id="6519b-217">Consumed immediately.</span></span> <span data-ttu-id="6519b-218">例如，在收到每个数据段时，数据可以立即存储到数据库中或写入磁盘。</span><span class="sxs-lookup"><span data-stu-id="6519b-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="6519b-219">以下文件上传程序类处理与客户端的 JS 互操作。</span><span class="sxs-lookup"><span data-stu-id="6519b-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="6519b-220">上传程序类使用 JS 互操作：</span><span class="sxs-lookup"><span data-stu-id="6519b-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="6519b-221">轮询客户端以发送数据段。</span><span class="sxs-lookup"><span data-stu-id="6519b-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="6519b-222">在轮询超时的情况下中止事务。</span><span class="sxs-lookup"><span data-stu-id="6519b-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="6519b-223">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="6519b-223">In the preceding example:</span></span>

* <span data-ttu-id="6519b-224">`maxBase64SegmentSize` 设置为 `8192`，其由 `maxBase64SegmentSize = segmentSize * 4 / 3` 计算得出。</span><span class="sxs-lookup"><span data-stu-id="6519b-224">The `maxBase64SegmentSize` is set to `8192`, which is calculated from `maxBase64SegmentSize = segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="6519b-225">低级别 .NET Core 内存管理 API 用于在 `uploadedSegments` 中的服务器上存储内存段。</span><span class="sxs-lookup"><span data-stu-id="6519b-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `uploadedSegments`.</span></span>
* <span data-ttu-id="6519b-226">`ReceiveFile` 方法用于处理通过 JS 互操作进行的上传：</span><span class="sxs-lookup"><span data-stu-id="6519b-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="6519b-227">通过与 `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` 的 JS 互操作确定文件大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="6519b-227">The file size is determined in bytes through JS interop with `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="6519b-228">在 `numberOfSegments` 中计算要接收的段数并进行存储。</span><span class="sxs-lookup"><span data-stu-id="6519b-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="6519b-229">通过与 `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` 的 JS 互操作在 `for` 循环中请求段。</span><span class="sxs-lookup"><span data-stu-id="6519b-229">The segments are requested in a `for` loop through JS interop with `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="6519b-230">除最后一个段外，所有段都必须为 8,192 字节才能进行解码。</span><span class="sxs-lookup"><span data-stu-id="6519b-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="6519b-231">客户端被强制以高效方式发送数据。</span><span class="sxs-lookup"><span data-stu-id="6519b-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="6519b-232">对于接收的每个段，在使用 <xref:System.Convert.TryFromBase64String%2A> 解码之前执行检查。</span><span class="sxs-lookup"><span data-stu-id="6519b-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String%2A>.</span></span>
  * <span data-ttu-id="6519b-233">上传完成后，包含数据的数据流会作为新的<xref:System.IO.Stream> (`SegmentedStream`) 返回。</span><span class="sxs-lookup"><span data-stu-id="6519b-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="6519b-234">分段的数据流类将段列表作为不可查找的只读 <xref:System.IO.Stream> 公开：</span><span class="sxs-lookup"><span data-stu-id="6519b-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="6519b-235">以下代码实现用于接收数据的 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="6519b-235">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
