---
title: 'ASP.NET Core :::no-loc(Blazor)::: 高级方案'
author: guardrex
description: '了解 :::no-loc(Blazor)::: 中的高级方案，包括如何将 RenderTreeBuilder 手动逻辑合并到应用中。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: 95714b3c0d21d3b348a9a8a984e2a42e7708499e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056551"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a><span data-ttu-id="baeb5-103">ASP.NET Core :::no-loc(Blazor)::: 高级方案</span><span class="sxs-lookup"><span data-stu-id="baeb5-103">ASP.NET Core :::no-loc(Blazor)::: advanced scenarios</span></span>

<span data-ttu-id="baeb5-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="baeb5-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="no-locblazor-server-circuit-handler"></a><span data-ttu-id="baeb5-105">:::no-loc(Blazor Server)::: 线路处理程序</span><span class="sxs-lookup"><span data-stu-id="baeb5-105">:::no-loc(Blazor Server)::: circuit handler</span></span>

<span data-ttu-id="baeb5-106">:::no-loc(Blazor Server)::: 允许代码定义线路处理程序，后者允许在用户线路的状态发生更改时运行代码。</span><span class="sxs-lookup"><span data-stu-id="baeb5-106">:::no-loc(Blazor Server)::: allows code to define a *circuit handler* , which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="baeb5-107">线路处理程序通过从 `CircuitHandler` 派生并在应用的服务容器中注册该类实现。</span><span class="sxs-lookup"><span data-stu-id="baeb5-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="baeb5-108">以下线路处理程序示例跟踪打开的 :::no-loc(SignalR)::: 连接：</span><span class="sxs-lookup"><span data-stu-id="baeb5-108">The following example of a circuit handler tracks open :::no-loc(SignalR)::: connections:</span></span>

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

<span data-ttu-id="baeb5-109">线路处理程序使用 DI 注册。</span><span class="sxs-lookup"><span data-stu-id="baeb5-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="baeb5-110">每个线路实例都会创建区分范围的实例。</span><span class="sxs-lookup"><span data-stu-id="baeb5-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="baeb5-111">借助前面示例中的 `TrackingCircuitHandler` 创建单一实例服务，因为必须跟踪所有线路的状态：</span><span class="sxs-lookup"><span data-stu-id="baeb5-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="baeb5-112">如果自定义线路处理程序的方法引发未处理异常，则该异常会导致 :::no-loc(Blazor Server)::: 线路产生严重错误。</span><span class="sxs-lookup"><span data-stu-id="baeb5-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the :::no-loc(Blazor Server)::: circuit.</span></span> <span data-ttu-id="baeb5-113">若要容忍处理程序代码或被调用方法中的异常，请使用错误处理和日志记录将代码包装到一个或多个 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句中。</span><span class="sxs-lookup"><span data-stu-id="baeb5-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="baeb5-114">当线路因用户断开连接而结束且框架正在清除线路状态时，框架会处置线路的 DI 范围。</span><span class="sxs-lookup"><span data-stu-id="baeb5-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="baeb5-115">处置范围时会处置所有实现 <xref:System.IDisposable?displayProperty=fullName> 的区分线路范围的 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="baeb5-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="baeb5-116">如果有任何 DI 服务在处置期间引发未处理异常，则框架会记录该异常。</span><span class="sxs-lookup"><span data-stu-id="baeb5-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="baeb5-117">RenderTreeBuilder 手动逻辑</span><span class="sxs-lookup"><span data-stu-id="baeb5-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="baeb5-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 提供用于操作组件和元素的方法，包括在 C# 代码中手动生成组件。</span><span class="sxs-lookup"><span data-stu-id="baeb5-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="baeb5-119">使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 创建组件是一种高级方案。</span><span class="sxs-lookup"><span data-stu-id="baeb5-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="baeb5-120">格式不正确的组件（例如，未封闭的标记标签）可能导致未定义的行为。</span><span class="sxs-lookup"><span data-stu-id="baeb5-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="baeb5-121">考虑以下 `PetDetails` 组件，可将其手动生成到另一个组件中：</span><span class="sxs-lookup"><span data-stu-id="baeb5-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="baeb5-122">在以下示例中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。</span><span class="sxs-lookup"><span data-stu-id="baeb5-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="baeb5-123">在具有序列号的 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法中，序列号是源代码行号。</span><span class="sxs-lookup"><span data-stu-id="baeb5-123">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="baeb5-124">:::no-loc(Blazor)::: 差分算法依赖于对应于不同代码行（而不是不同调用的调用）的序列号。</span><span class="sxs-lookup"><span data-stu-id="baeb5-124">The :::no-loc(Blazor)::: difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="baeb5-125">使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法创建组件时，请对序列号的参数进行硬编码。</span><span class="sxs-lookup"><span data-stu-id="baeb5-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="baeb5-126">**通过计算或计数器生成序列号可能导致性能不佳。**</span><span class="sxs-lookup"><span data-stu-id="baeb5-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="baeb5-127">有关详细信息，请参阅[序列号与代码行号相关，而不与执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)部分。</span><span class="sxs-lookup"><span data-stu-id="baeb5-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="baeb5-128">`BuiltContent` 组件：</span><span class="sxs-lookup"><span data-stu-id="baeb5-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="baeb5-129"><xref:Microsoft.AspNetCore.Components.RenderTree> 中的类型允许处理呈现操作的 *结果* 。</span><span class="sxs-lookup"><span data-stu-id="baeb5-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="baeb5-130">这些是 :::no-loc(Blazor)::: 框架实现的内部细节。</span><span class="sxs-lookup"><span data-stu-id="baeb5-130">These are internal details of the :::no-loc(Blazor)::: framework implementation.</span></span> <span data-ttu-id="baeb5-131">这些类型应视为 *不稳定* ，并且在未来版本中可能会有更改。</span><span class="sxs-lookup"><span data-stu-id="baeb5-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="baeb5-132">序列号与代码行号相关，而不与执行顺序相关</span><span class="sxs-lookup"><span data-stu-id="baeb5-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="baeb5-133">:::no-loc(Razor)::: 组件文件 (`.razor`) 始终被编译。</span><span class="sxs-lookup"><span data-stu-id="baeb5-133">:::no-loc(Razor)::: component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="baeb5-134">与解释代码相比，编译具有潜在优势，因为编译步骤可用于注入信息，从而在运行时提高应用性能。</span><span class="sxs-lookup"><span data-stu-id="baeb5-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="baeb5-135">这些改进的关键示例涉及 *序列号* 。</span><span class="sxs-lookup"><span data-stu-id="baeb5-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="baeb5-136">序列号向运行时指示哪些输出来自哪些不同的已排序代码行。</span><span class="sxs-lookup"><span data-stu-id="baeb5-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="baeb5-137">运行时使用此信息在线性时间内生成高效的树上差分，这比常规树上差分算法通常可以做到的速度快得多。</span><span class="sxs-lookup"><span data-stu-id="baeb5-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="baeb5-138">请考虑使用以下 :::no-loc(Razor)::: 组件（`.razor` 文件）：</span><span class="sxs-lookup"><span data-stu-id="baeb5-138">Consider the following :::no-loc(Razor)::: component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="baeb5-139">前面的代码编译为以下所示内容：</span><span class="sxs-lookup"><span data-stu-id="baeb5-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="baeb5-140">首次执行代码时，如果 `someFlag` 为 `true`，则生成器会收到：</span><span class="sxs-lookup"><span data-stu-id="baeb5-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="baeb5-141">序列</span><span class="sxs-lookup"><span data-stu-id="baeb5-141">Sequence</span></span> | <span data-ttu-id="baeb5-142">类型</span><span class="sxs-lookup"><span data-stu-id="baeb5-142">Type</span></span>      | <span data-ttu-id="baeb5-143">数据</span><span class="sxs-lookup"><span data-stu-id="baeb5-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="baeb5-144">0</span><span class="sxs-lookup"><span data-stu-id="baeb5-144">0</span></span>        | <span data-ttu-id="baeb5-145">Text 节点</span><span class="sxs-lookup"><span data-stu-id="baeb5-145">Text node</span></span> | <span data-ttu-id="baeb5-146">First</span><span class="sxs-lookup"><span data-stu-id="baeb5-146">First</span></span>  |
| <span data-ttu-id="baeb5-147">1</span><span class="sxs-lookup"><span data-stu-id="baeb5-147">1</span></span>        | <span data-ttu-id="baeb5-148">Text 节点</span><span class="sxs-lookup"><span data-stu-id="baeb5-148">Text node</span></span> | <span data-ttu-id="baeb5-149">秒</span><span class="sxs-lookup"><span data-stu-id="baeb5-149">Second</span></span> |

<span data-ttu-id="baeb5-150">假设 `someFlag` 变为 `false` 且标记再次呈现。</span><span class="sxs-lookup"><span data-stu-id="baeb5-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="baeb5-151">此时，生成器会收到：</span><span class="sxs-lookup"><span data-stu-id="baeb5-151">This time, the builder receives:</span></span>

| <span data-ttu-id="baeb5-152">序列</span><span class="sxs-lookup"><span data-stu-id="baeb5-152">Sequence</span></span> | <span data-ttu-id="baeb5-153">类型</span><span class="sxs-lookup"><span data-stu-id="baeb5-153">Type</span></span>       | <span data-ttu-id="baeb5-154">数据</span><span class="sxs-lookup"><span data-stu-id="baeb5-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="baeb5-155">1</span><span class="sxs-lookup"><span data-stu-id="baeb5-155">1</span></span>        | <span data-ttu-id="baeb5-156">Text 节点</span><span class="sxs-lookup"><span data-stu-id="baeb5-156">Text node</span></span>  | <span data-ttu-id="baeb5-157">秒</span><span class="sxs-lookup"><span data-stu-id="baeb5-157">Second</span></span> |

<span data-ttu-id="baeb5-158">当运行时执行差分时，它会看到序列 `0` 处的项目已被删除，因此，它会生成以下普通 *编辑脚本* ：</span><span class="sxs-lookup"><span data-stu-id="baeb5-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script* :</span></span>

* <span data-ttu-id="baeb5-159">删除第一个文本节点。</span><span class="sxs-lookup"><span data-stu-id="baeb5-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="baeb5-160">以编程方式生成序列号的问题</span><span class="sxs-lookup"><span data-stu-id="baeb5-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="baeb5-161">想象一下，你编写了以下呈现树生成器逻辑：</span><span class="sxs-lookup"><span data-stu-id="baeb5-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="baeb5-162">现在，第一个输出是：</span><span class="sxs-lookup"><span data-stu-id="baeb5-162">Now, the first output is:</span></span>

| <span data-ttu-id="baeb5-163">序列</span><span class="sxs-lookup"><span data-stu-id="baeb5-163">Sequence</span></span> | <span data-ttu-id="baeb5-164">类型</span><span class="sxs-lookup"><span data-stu-id="baeb5-164">Type</span></span>      | <span data-ttu-id="baeb5-165">数据</span><span class="sxs-lookup"><span data-stu-id="baeb5-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="baeb5-166">0</span><span class="sxs-lookup"><span data-stu-id="baeb5-166">0</span></span>        | <span data-ttu-id="baeb5-167">Text 节点</span><span class="sxs-lookup"><span data-stu-id="baeb5-167">Text node</span></span> | <span data-ttu-id="baeb5-168">First</span><span class="sxs-lookup"><span data-stu-id="baeb5-168">First</span></span>  |
| <span data-ttu-id="baeb5-169">1</span><span class="sxs-lookup"><span data-stu-id="baeb5-169">1</span></span>        | <span data-ttu-id="baeb5-170">Text 节点</span><span class="sxs-lookup"><span data-stu-id="baeb5-170">Text node</span></span> | <span data-ttu-id="baeb5-171">秒</span><span class="sxs-lookup"><span data-stu-id="baeb5-171">Second</span></span> |

<span data-ttu-id="baeb5-172">此结果与之前的示例相同，因此不存在负面问题。</span><span class="sxs-lookup"><span data-stu-id="baeb5-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="baeb5-173">在第二个呈现中，`someFlag` 为 `false`，输出为：</span><span class="sxs-lookup"><span data-stu-id="baeb5-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="baeb5-174">序列</span><span class="sxs-lookup"><span data-stu-id="baeb5-174">Sequence</span></span> | <span data-ttu-id="baeb5-175">类型</span><span class="sxs-lookup"><span data-stu-id="baeb5-175">Type</span></span>      | <span data-ttu-id="baeb5-176">数据</span><span class="sxs-lookup"><span data-stu-id="baeb5-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="baeb5-177">0</span><span class="sxs-lookup"><span data-stu-id="baeb5-177">0</span></span>        | <span data-ttu-id="baeb5-178">Text 节点</span><span class="sxs-lookup"><span data-stu-id="baeb5-178">Text node</span></span> | <span data-ttu-id="baeb5-179">秒</span><span class="sxs-lookup"><span data-stu-id="baeb5-179">Second</span></span> |

<span data-ttu-id="baeb5-180">此时，差分算法发现发生了 *两个* 变化，且算法生成以下编辑脚本：</span><span class="sxs-lookup"><span data-stu-id="baeb5-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="baeb5-181">将第一个文本节点的值更改为 `Second`。</span><span class="sxs-lookup"><span data-stu-id="baeb5-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="baeb5-182">删除第二个文本节点。</span><span class="sxs-lookup"><span data-stu-id="baeb5-182">Remove the second text node.</span></span>

<span data-ttu-id="baeb5-183">生成序列号会丢失有关原始代码中 `if/else` 分支和循环的位置的所有有用信息。</span><span class="sxs-lookup"><span data-stu-id="baeb5-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="baeb5-184">这会导致 **两倍于** 之前长度的差异。</span><span class="sxs-lookup"><span data-stu-id="baeb5-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="baeb5-185">这是一个普通示例。</span><span class="sxs-lookup"><span data-stu-id="baeb5-185">This is a trivial example.</span></span> <span data-ttu-id="baeb5-186">在具有深度嵌套的复杂结构（尤其是带有循环）的更真实的情况下，性能成本通常会更高。</span><span class="sxs-lookup"><span data-stu-id="baeb5-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="baeb5-187">差分算法必须深入递归到呈现树中，而不是立即确定已插入或删除的循环块或分支。</span><span class="sxs-lookup"><span data-stu-id="baeb5-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="baeb5-188">这通常导致必须生成更长的编辑脚本，因为差分算法获知了关于新旧结构之间关系的错误信息。</span><span class="sxs-lookup"><span data-stu-id="baeb5-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="baeb5-189">指南和结论</span><span class="sxs-lookup"><span data-stu-id="baeb5-189">Guidance and conclusions</span></span>

* <span data-ttu-id="baeb5-190">如果动态生成序列号，则应用性能会受到影响。</span><span class="sxs-lookup"><span data-stu-id="baeb5-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="baeb5-191">该框架无法在运行时自动创建自己的序列号，因为除非在编译时捕获了必需的信息，否则这些信息不存在。</span><span class="sxs-lookup"><span data-stu-id="baeb5-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="baeb5-192">不要编写手动实现的冗长 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 逻辑块。</span><span class="sxs-lookup"><span data-stu-id="baeb5-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="baeb5-193">优先使用 `.razor` 文件并允许编译器处理序列号。</span><span class="sxs-lookup"><span data-stu-id="baeb5-193">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="baeb5-194">如果无法避免 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 手动逻辑，请将较长的代码块拆分为封装在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 调用中的较小部分。</span><span class="sxs-lookup"><span data-stu-id="baeb5-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="baeb5-195">每个区域都有自己的独立序列号空间，因此可在每个区域内从零（或任何其他任意数）重新开始。</span><span class="sxs-lookup"><span data-stu-id="baeb5-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="baeb5-196">如果序列号已硬编码，则差分算法仅要求序列号的值增加。</span><span class="sxs-lookup"><span data-stu-id="baeb5-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="baeb5-197">初始值和间隔不相关。</span><span class="sxs-lookup"><span data-stu-id="baeb5-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="baeb5-198">一个合理选择是使用代码行号作为序列号，或者从零开始并以 1 或 100 的间隔（或任何首选间隔）增加。</span><span class="sxs-lookup"><span data-stu-id="baeb5-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="baeb5-199">:::no-loc(Blazor)::: 使用序列号，而其他树上差分 UI 框架不使用它们。</span><span class="sxs-lookup"><span data-stu-id="baeb5-199">:::no-loc(Blazor)::: uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="baeb5-200">使用序列号时，差分速度要快得多，并且 :::no-loc(Blazor)::: 的优势在于编译步骤可为编写 `.razor` 文件的开发人员自动处理序列号。</span><span class="sxs-lookup"><span data-stu-id="baeb5-200">Diffing is far faster when sequence numbers are used, and :::no-loc(Blazor)::: has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
