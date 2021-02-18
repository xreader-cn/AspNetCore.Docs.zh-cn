---
title: ASP.NET Core Blazor 高级方案
author: guardrex
description: 了解 Blazor 中的高级方案，包括如何将 RenderTreeBuilder 手动逻辑合并到应用中。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: ba2bf91f3318225383ec9d164c34be9124aa311b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280854"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="02b13-103">ASP.NET Core Blazor 高级方案</span><span class="sxs-lookup"><span data-stu-id="02b13-103">ASP.NET Core Blazor advanced scenarios</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="02b13-104">Blazor Server 线路处理程序</span><span class="sxs-lookup"><span data-stu-id="02b13-104">Blazor Server circuit handler</span></span>

<span data-ttu-id="02b13-105">Blazor Server 允许代码定义线路处理程序，后者允许在用户线路的状态发生更改时运行代码。</span><span class="sxs-lookup"><span data-stu-id="02b13-105">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="02b13-106">线路处理程序通过从 `CircuitHandler` 派生并在应用的服务容器中注册该类实现。</span><span class="sxs-lookup"><span data-stu-id="02b13-106">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="02b13-107">以下线路处理程序示例跟踪打开的 SignalR 连接：</span><span class="sxs-lookup"><span data-stu-id="02b13-107">The following example of a circuit handler tracks open SignalR connections:</span></span>

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

<span data-ttu-id="02b13-108">线路处理程序使用 DI 注册。</span><span class="sxs-lookup"><span data-stu-id="02b13-108">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="02b13-109">每个线路实例都会创建区分范围的实例。</span><span class="sxs-lookup"><span data-stu-id="02b13-109">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="02b13-110">借助前面示例中的 `TrackingCircuitHandler` 创建单一实例服务，因为必须跟踪所有线路的状态：</span><span class="sxs-lookup"><span data-stu-id="02b13-110">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="02b13-111">如果自定义线路处理程序的方法引发未处理异常，则该异常会导致 Blazor Server 线路产生严重错误。</span><span class="sxs-lookup"><span data-stu-id="02b13-111">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="02b13-112">若要容忍处理程序代码或被调用方法中的异常，请使用错误处理和日志记录将代码包装到一个或多个 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句中。</span><span class="sxs-lookup"><span data-stu-id="02b13-112">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="02b13-113">当线路因用户断开连接而结束且框架正在清除线路状态时，框架会处置线路的 DI 范围。</span><span class="sxs-lookup"><span data-stu-id="02b13-113">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="02b13-114">处置范围时会处置所有实现 <xref:System.IDisposable?displayProperty=fullName> 的区分线路范围的 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="02b13-114">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="02b13-115">如果有任何 DI 服务在处置期间引发未处理异常，则框架会记录该异常。</span><span class="sxs-lookup"><span data-stu-id="02b13-115">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="02b13-116">RenderTreeBuilder 手动逻辑</span><span class="sxs-lookup"><span data-stu-id="02b13-116">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="02b13-117"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 提供用于操作组件和元素的方法，包括在 C# 代码中手动生成组件。</span><span class="sxs-lookup"><span data-stu-id="02b13-117"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="02b13-118">使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 创建组件是一种高级方案。</span><span class="sxs-lookup"><span data-stu-id="02b13-118">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="02b13-119">格式不正确的组件（例如，未封闭的标记标签）可能导致未定义的行为。</span><span class="sxs-lookup"><span data-stu-id="02b13-119">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="02b13-120">考虑以下 `PetDetails` 组件，可将其手动生成到另一个组件中：</span><span class="sxs-lookup"><span data-stu-id="02b13-120">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="02b13-121">在以下示例中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。</span><span class="sxs-lookup"><span data-stu-id="02b13-121">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="02b13-122">在具有序列号的 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法中，序列号是源代码行号。</span><span class="sxs-lookup"><span data-stu-id="02b13-122">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="02b13-123">Blazor 差分算法依赖于对应于不同代码行（而不是不同调用的调用）的序列号。</span><span class="sxs-lookup"><span data-stu-id="02b13-123">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="02b13-124">使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法创建组件时，请对序列号的参数进行硬编码。</span><span class="sxs-lookup"><span data-stu-id="02b13-124">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="02b13-125">**通过计算或计数器生成序列号可能导致性能不佳。**</span><span class="sxs-lookup"><span data-stu-id="02b13-125">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="02b13-126">有关详细信息，请参阅[序列号与代码行号相关，而不与执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)部分。</span><span class="sxs-lookup"><span data-stu-id="02b13-126">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="02b13-127">`BuiltContent` 组件：</span><span class="sxs-lookup"><span data-stu-id="02b13-127">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="02b13-128"><xref:Microsoft.AspNetCore.Components.RenderTree> 中的类型允许处理呈现操作的 *结果*。</span><span class="sxs-lookup"><span data-stu-id="02b13-128">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="02b13-129">这些是 Blazor 框架实现的内部细节。</span><span class="sxs-lookup"><span data-stu-id="02b13-129">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="02b13-130">这些类型应视为 *不稳定*，并且在未来版本中可能会有更改。</span><span class="sxs-lookup"><span data-stu-id="02b13-130">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="02b13-131">序列号与代码行号相关，而不与执行顺序相关</span><span class="sxs-lookup"><span data-stu-id="02b13-131">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="02b13-132">Razor 组件文件 (`.razor`) 始终被编译。</span><span class="sxs-lookup"><span data-stu-id="02b13-132">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="02b13-133">与解释代码相比，编译具有潜在优势，因为编译步骤可用于注入信息，从而在运行时提高应用性能。</span><span class="sxs-lookup"><span data-stu-id="02b13-133">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="02b13-134">这些改进的关键示例涉及 *序列号*。</span><span class="sxs-lookup"><span data-stu-id="02b13-134">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="02b13-135">序列号向运行时指示哪些输出来自哪些不同的已排序代码行。</span><span class="sxs-lookup"><span data-stu-id="02b13-135">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="02b13-136">运行时使用此信息在线性时间内生成高效的树上差分，这比常规树上差分算法通常可以做到的速度快得多。</span><span class="sxs-lookup"><span data-stu-id="02b13-136">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="02b13-137">请考虑使用以下 Razor 组件（`.razor` 文件）：</span><span class="sxs-lookup"><span data-stu-id="02b13-137">Consider the following Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="02b13-138">前面的代码编译为以下所示内容：</span><span class="sxs-lookup"><span data-stu-id="02b13-138">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="02b13-139">首次执行代码时，如果 `someFlag` 为 `true`，则生成器会收到：</span><span class="sxs-lookup"><span data-stu-id="02b13-139">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="02b13-140">序列</span><span class="sxs-lookup"><span data-stu-id="02b13-140">Sequence</span></span> | <span data-ttu-id="02b13-141">类型</span><span class="sxs-lookup"><span data-stu-id="02b13-141">Type</span></span>      | <span data-ttu-id="02b13-142">数据</span><span class="sxs-lookup"><span data-stu-id="02b13-142">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="02b13-143">0</span><span class="sxs-lookup"><span data-stu-id="02b13-143">0</span></span>        | <span data-ttu-id="02b13-144">Text 节点</span><span class="sxs-lookup"><span data-stu-id="02b13-144">Text node</span></span> | <span data-ttu-id="02b13-145">First</span><span class="sxs-lookup"><span data-stu-id="02b13-145">First</span></span>  |
| <span data-ttu-id="02b13-146">1</span><span class="sxs-lookup"><span data-stu-id="02b13-146">1</span></span>        | <span data-ttu-id="02b13-147">Text 节点</span><span class="sxs-lookup"><span data-stu-id="02b13-147">Text node</span></span> | <span data-ttu-id="02b13-148">秒</span><span class="sxs-lookup"><span data-stu-id="02b13-148">Second</span></span> |

<span data-ttu-id="02b13-149">假设 `someFlag` 变为 `false` 且标记再次呈现。</span><span class="sxs-lookup"><span data-stu-id="02b13-149">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="02b13-150">此时，生成器会收到：</span><span class="sxs-lookup"><span data-stu-id="02b13-150">This time, the builder receives:</span></span>

| <span data-ttu-id="02b13-151">序列</span><span class="sxs-lookup"><span data-stu-id="02b13-151">Sequence</span></span> | <span data-ttu-id="02b13-152">类型</span><span class="sxs-lookup"><span data-stu-id="02b13-152">Type</span></span>       | <span data-ttu-id="02b13-153">数据</span><span class="sxs-lookup"><span data-stu-id="02b13-153">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="02b13-154">1</span><span class="sxs-lookup"><span data-stu-id="02b13-154">1</span></span>        | <span data-ttu-id="02b13-155">Text 节点</span><span class="sxs-lookup"><span data-stu-id="02b13-155">Text node</span></span>  | <span data-ttu-id="02b13-156">秒</span><span class="sxs-lookup"><span data-stu-id="02b13-156">Second</span></span> |

<span data-ttu-id="02b13-157">当运行时执行差分时，它会看到序列 `0` 处的项目已被删除，因此，它会生成以下普通 *编辑脚本*：</span><span class="sxs-lookup"><span data-stu-id="02b13-157">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="02b13-158">删除第一个文本节点。</span><span class="sxs-lookup"><span data-stu-id="02b13-158">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="02b13-159">以编程方式生成序列号的问题</span><span class="sxs-lookup"><span data-stu-id="02b13-159">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="02b13-160">想象一下，你编写了以下呈现树生成器逻辑：</span><span class="sxs-lookup"><span data-stu-id="02b13-160">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="02b13-161">现在，第一个输出是：</span><span class="sxs-lookup"><span data-stu-id="02b13-161">Now, the first output is:</span></span>

| <span data-ttu-id="02b13-162">序列</span><span class="sxs-lookup"><span data-stu-id="02b13-162">Sequence</span></span> | <span data-ttu-id="02b13-163">类型</span><span class="sxs-lookup"><span data-stu-id="02b13-163">Type</span></span>      | <span data-ttu-id="02b13-164">数据</span><span class="sxs-lookup"><span data-stu-id="02b13-164">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="02b13-165">0</span><span class="sxs-lookup"><span data-stu-id="02b13-165">0</span></span>        | <span data-ttu-id="02b13-166">Text 节点</span><span class="sxs-lookup"><span data-stu-id="02b13-166">Text node</span></span> | <span data-ttu-id="02b13-167">First</span><span class="sxs-lookup"><span data-stu-id="02b13-167">First</span></span>  |
| <span data-ttu-id="02b13-168">1</span><span class="sxs-lookup"><span data-stu-id="02b13-168">1</span></span>        | <span data-ttu-id="02b13-169">Text 节点</span><span class="sxs-lookup"><span data-stu-id="02b13-169">Text node</span></span> | <span data-ttu-id="02b13-170">秒</span><span class="sxs-lookup"><span data-stu-id="02b13-170">Second</span></span> |

<span data-ttu-id="02b13-171">此结果与之前的示例相同，因此不存在负面问题。</span><span class="sxs-lookup"><span data-stu-id="02b13-171">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="02b13-172">在第二个呈现中，`someFlag` 为 `false`，输出为：</span><span class="sxs-lookup"><span data-stu-id="02b13-172">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="02b13-173">序列</span><span class="sxs-lookup"><span data-stu-id="02b13-173">Sequence</span></span> | <span data-ttu-id="02b13-174">类型</span><span class="sxs-lookup"><span data-stu-id="02b13-174">Type</span></span>      | <span data-ttu-id="02b13-175">数据</span><span class="sxs-lookup"><span data-stu-id="02b13-175">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="02b13-176">0</span><span class="sxs-lookup"><span data-stu-id="02b13-176">0</span></span>        | <span data-ttu-id="02b13-177">Text 节点</span><span class="sxs-lookup"><span data-stu-id="02b13-177">Text node</span></span> | <span data-ttu-id="02b13-178">秒</span><span class="sxs-lookup"><span data-stu-id="02b13-178">Second</span></span> |

<span data-ttu-id="02b13-179">此时，差分算法发现发生了 *两个* 变化，且算法生成以下编辑脚本：</span><span class="sxs-lookup"><span data-stu-id="02b13-179">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="02b13-180">将第一个文本节点的值更改为 `Second`。</span><span class="sxs-lookup"><span data-stu-id="02b13-180">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="02b13-181">删除第二个文本节点。</span><span class="sxs-lookup"><span data-stu-id="02b13-181">Remove the second text node.</span></span>

<span data-ttu-id="02b13-182">生成序列号会丢失有关原始代码中 `if/else` 分支和循环的位置的所有有用信息。</span><span class="sxs-lookup"><span data-stu-id="02b13-182">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="02b13-183">这会导致 **两倍于** 之前长度的差异。</span><span class="sxs-lookup"><span data-stu-id="02b13-183">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="02b13-184">这是一个普通示例。</span><span class="sxs-lookup"><span data-stu-id="02b13-184">This is a trivial example.</span></span> <span data-ttu-id="02b13-185">在具有深度嵌套的复杂结构（尤其是带有循环）的更真实的情况下，性能成本通常会更高。</span><span class="sxs-lookup"><span data-stu-id="02b13-185">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="02b13-186">差分算法必须深入递归到呈现树中，而不是立即确定已插入或删除的循环块或分支。</span><span class="sxs-lookup"><span data-stu-id="02b13-186">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="02b13-187">这通常导致必须生成更长的编辑脚本，因为差分算法获知了关于新旧结构之间关系的错误信息。</span><span class="sxs-lookup"><span data-stu-id="02b13-187">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="02b13-188">指南和结论</span><span class="sxs-lookup"><span data-stu-id="02b13-188">Guidance and conclusions</span></span>

* <span data-ttu-id="02b13-189">如果动态生成序列号，则应用性能会受到影响。</span><span class="sxs-lookup"><span data-stu-id="02b13-189">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="02b13-190">该框架无法在运行时自动创建自己的序列号，因为除非在编译时捕获了必需的信息，否则这些信息不存在。</span><span class="sxs-lookup"><span data-stu-id="02b13-190">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="02b13-191">不要编写手动实现的冗长 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 逻辑块。</span><span class="sxs-lookup"><span data-stu-id="02b13-191">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="02b13-192">优先使用 `.razor` 文件并允许编译器处理序列号。</span><span class="sxs-lookup"><span data-stu-id="02b13-192">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="02b13-193">如果无法避免 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 手动逻辑，请将较长的代码块拆分为封装在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 调用中的较小部分。</span><span class="sxs-lookup"><span data-stu-id="02b13-193">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="02b13-194">每个区域都有自己的独立序列号空间，因此可在每个区域内从零（或任何其他任意数）重新开始。</span><span class="sxs-lookup"><span data-stu-id="02b13-194">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="02b13-195">如果序列号已硬编码，则差分算法仅要求序列号的值增加。</span><span class="sxs-lookup"><span data-stu-id="02b13-195">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="02b13-196">初始值和间隔不相关。</span><span class="sxs-lookup"><span data-stu-id="02b13-196">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="02b13-197">一个合理选择是使用代码行号作为序列号，或者从零开始并以 1 或 100 的间隔（或任何首选间隔）增加。</span><span class="sxs-lookup"><span data-stu-id="02b13-197">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="02b13-198">Blazor 使用序列号，而其他树上差分 UI 框架不使用它们。</span><span class="sxs-lookup"><span data-stu-id="02b13-198">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="02b13-199">使用序列号时，差分速度要快得多，并且 Blazor 的优势在于编译步骤可为编写 `.razor` 文件的开发人员自动处理序列号。</span><span class="sxs-lookup"><span data-stu-id="02b13-199">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
