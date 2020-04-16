---
title: ASP.NET核心中的内存管理和模式
author: rick-anderson
description: 了解如何在ASP.NET核心中管理内存，以及垃圾回收器 （GC） 的工作原理。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440943"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="bb93c-103">ASP.NET核心中的内存管理和垃圾回收 （GC）</span><span class="sxs-lookup"><span data-stu-id="bb93c-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="bb93c-104">由[塞巴斯蒂安·罗斯](https://github.com/sebastienros)和[里克·安德森](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bb93c-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bb93c-105">内存管理很复杂，即使在像 .NET 这样的托管框架中也是如此。</span><span class="sxs-lookup"><span data-stu-id="bb93c-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="bb93c-106">分析和理解内存问题可能具有挑战性。</span><span class="sxs-lookup"><span data-stu-id="bb93c-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="bb93c-107">本文：</span><span class="sxs-lookup"><span data-stu-id="bb93c-107">This article:</span></span>

* <span data-ttu-id="bb93c-108">受许多*内存泄漏*和*GC 不工作*问题引起的。</span><span class="sxs-lookup"><span data-stu-id="bb93c-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="bb93c-109">这些问题大多是由于不了解内存消耗在 .NET Core 中的工作方式，或者不了解如何测量内存消耗造成的。</span><span class="sxs-lookup"><span data-stu-id="bb93c-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="bb93c-110">演示有问题的内存使用，并建议替代方法。</span><span class="sxs-lookup"><span data-stu-id="bb93c-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="bb93c-111">垃圾回收 （GC） 在 .NET 核心中的工作方式</span><span class="sxs-lookup"><span data-stu-id="bb93c-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="bb93c-112">GC 分配堆段，其中每个段是连续的内存范围。</span><span class="sxs-lookup"><span data-stu-id="bb93c-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="bb93c-113">放置在堆中的对象分为 3 代之一：0、1 或 2。</span><span class="sxs-lookup"><span data-stu-id="bb93c-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="bb93c-114">生成确定 GC 尝试释放应用程序不再引用的托管对象上的内存的频率。</span><span class="sxs-lookup"><span data-stu-id="bb93c-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="bb93c-115">编号较低的代是 GC 更频繁地使用。</span><span class="sxs-lookup"><span data-stu-id="bb93c-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="bb93c-116">对象根据其生存期从一代移动到另一代。</span><span class="sxs-lookup"><span data-stu-id="bb93c-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="bb93c-117">随着对象寿命的延长，它们被移动到更高的一代。</span><span class="sxs-lookup"><span data-stu-id="bb93c-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="bb93c-118">如前所述，较高一代是 GC 的较少频率。</span><span class="sxs-lookup"><span data-stu-id="bb93c-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="bb93c-119">短期生存对象始终保留在第 0 代中。</span><span class="sxs-lookup"><span data-stu-id="bb93c-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="bb93c-120">例如，在 Web 请求期间引用的对象是短暂的。</span><span class="sxs-lookup"><span data-stu-id="bb93c-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="bb93c-121">应用程序级[单例](xref:fundamentals/dependency-injection#service-lifetimes)通常迁移到第 2 代。</span><span class="sxs-lookup"><span data-stu-id="bb93c-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="bb93c-122">当 ASP.NET 核心应用启动时，GC：</span><span class="sxs-lookup"><span data-stu-id="bb93c-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="bb93c-123">为初始堆段保留一些内存。</span><span class="sxs-lookup"><span data-stu-id="bb93c-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="bb93c-124">加载运行时时提交一小部分内存。</span><span class="sxs-lookup"><span data-stu-id="bb93c-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="bb93c-125">出于性能原因，上述内存分配完成。</span><span class="sxs-lookup"><span data-stu-id="bb93c-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="bb93c-126">性能优势来自连续内存中的堆段。</span><span class="sxs-lookup"><span data-stu-id="bb93c-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="bb93c-127">调用 GC。收集</span><span class="sxs-lookup"><span data-stu-id="bb93c-127">Call GC.Collect</span></span>

<span data-ttu-id="bb93c-128">调用[GC。显式收集](xref:System.GC.Collect*)：</span><span class="sxs-lookup"><span data-stu-id="bb93c-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="bb93c-129">**不应**通过生产ASP.NET核心应用来完成。</span><span class="sxs-lookup"><span data-stu-id="bb93c-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="bb93c-130">在调查内存泄漏时很有用。</span><span class="sxs-lookup"><span data-stu-id="bb93c-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="bb93c-131">调查时，验证 GC 从内存中删除了所有悬空对象，以便可以测量内存。</span><span class="sxs-lookup"><span data-stu-id="bb93c-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="bb93c-132">分析应用的内存使用情况</span><span class="sxs-lookup"><span data-stu-id="bb93c-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="bb93c-133">专用工具可帮助分析内存使用情况：</span><span class="sxs-lookup"><span data-stu-id="bb93c-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="bb93c-134">计数对象引用</span><span class="sxs-lookup"><span data-stu-id="bb93c-134">Counting object references</span></span>
- <span data-ttu-id="bb93c-135">测量 GC 对 CPU 使用率的影响</span><span class="sxs-lookup"><span data-stu-id="bb93c-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="bb93c-136">测量每一代使用的内存空间</span><span class="sxs-lookup"><span data-stu-id="bb93c-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="bb93c-137">使用以下工具分析内存使用情况：</span><span class="sxs-lookup"><span data-stu-id="bb93c-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="bb93c-138">[点网跟踪](/dotnet/core/diagnostics/dotnet-trace)：可用于生产机器。</span><span class="sxs-lookup"><span data-stu-id="bb93c-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="bb93c-139">分析不使用 Visual Studio 调试器情况下的内存使用情况</span><span class="sxs-lookup"><span data-stu-id="bb93c-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="bb93c-140">Visual Studio 中的配置文件内存使用</span><span class="sxs-lookup"><span data-stu-id="bb93c-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="bb93c-141">检测内存问题</span><span class="sxs-lookup"><span data-stu-id="bb93c-141">Detecting memory issues</span></span>

<span data-ttu-id="bb93c-142">任务管理器可用于了解应用使用的ASP.NET内存量。</span><span class="sxs-lookup"><span data-stu-id="bb93c-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="bb93c-143">任务管理器内存值：</span><span class="sxs-lookup"><span data-stu-id="bb93c-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="bb93c-144">表示ASP.NET进程使用的内存量。</span><span class="sxs-lookup"><span data-stu-id="bb93c-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="bb93c-145">包括应用的活对象和其他内存使用者，如本机内存使用情况。</span><span class="sxs-lookup"><span data-stu-id="bb93c-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="bb93c-146">如果任务管理器内存值无限增加且从不变展，则应用会泄漏内存。</span><span class="sxs-lookup"><span data-stu-id="bb93c-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="bb93c-147">以下各节演示并解释几种内存使用模式。</span><span class="sxs-lookup"><span data-stu-id="bb93c-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="bb93c-148">示例显示内存使用情况应用</span><span class="sxs-lookup"><span data-stu-id="bb93c-148">Sample display memory usage app</span></span>

<span data-ttu-id="bb93c-149">[内存泄漏示例应用](https://github.com/sebastienros/memoryleak)在 GitHub 上可用。</span><span class="sxs-lookup"><span data-stu-id="bb93c-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="bb93c-150">内存泄漏应用：</span><span class="sxs-lookup"><span data-stu-id="bb93c-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="bb93c-151">包括一个诊断控制器，用于收集应用的实时内存和 GC 数据。</span><span class="sxs-lookup"><span data-stu-id="bb93c-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="bb93c-152">具有显示内存和 GC 数据的索引页。</span><span class="sxs-lookup"><span data-stu-id="bb93c-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="bb93c-153">每秒刷新一次索引页。</span><span class="sxs-lookup"><span data-stu-id="bb93c-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="bb93c-154">包含一个 API 控制器，该控制器提供各种内存加载模式。</span><span class="sxs-lookup"><span data-stu-id="bb93c-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="bb93c-155">不是受支持的工具，但是，它可用于显示ASP.NET核心应用的内存使用模式。</span><span class="sxs-lookup"><span data-stu-id="bb93c-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="bb93c-156">运行内存泄漏。</span><span class="sxs-lookup"><span data-stu-id="bb93c-156">Run MemoryLeak.</span></span> <span data-ttu-id="bb93c-157">分配的内存会缓慢增加，直到发生 GC。</span><span class="sxs-lookup"><span data-stu-id="bb93c-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="bb93c-158">内存增加，因为该工具分配自定义对象以捕获数据。</span><span class="sxs-lookup"><span data-stu-id="bb93c-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="bb93c-159">下图显示了发生第 0 代 GC 时内存泄漏索引页。</span><span class="sxs-lookup"><span data-stu-id="bb93c-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="bb93c-160">该图表显示 0 RPS（每秒请求），因为未调用来自 API 控制器的 API 终结点。</span><span class="sxs-lookup"><span data-stu-id="bb93c-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![前一图表](memory/_static/0RPS.png)

<span data-ttu-id="bb93c-162">图表显示内存使用情况的两个值：</span><span class="sxs-lookup"><span data-stu-id="bb93c-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="bb93c-163">已分配：托管对象占用的内存量</span><span class="sxs-lookup"><span data-stu-id="bb93c-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="bb93c-164">[工作集](/windows/win32/memory/working-set)：进程虚拟地址空间中的页面集，当前驻留在物理内存中。</span><span class="sxs-lookup"><span data-stu-id="bb93c-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="bb93c-165">显示的工作集与任务管理器显示的值相同。</span><span class="sxs-lookup"><span data-stu-id="bb93c-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="bb93c-166">瞬态对象</span><span class="sxs-lookup"><span data-stu-id="bb93c-166">Transient objects</span></span>

<span data-ttu-id="bb93c-167">以下 API 创建一个 10 KB 字符串实例并将其返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="bb93c-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="bb93c-168">在每个请求上，一个新对象在内存中分配并写入响应。</span><span class="sxs-lookup"><span data-stu-id="bb93c-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="bb93c-169">字符串在 .NET 中存储为 UTF-16 字符，因此每个字符在内存中需要 2 个字节。</span><span class="sxs-lookup"><span data-stu-id="bb93c-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="bb93c-170">以下图形的负载相对较小，以显示内存分配如何受到 GC 的影响。</span><span class="sxs-lookup"><span data-stu-id="bb93c-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![前一图表](memory/_static/bigstring.png)

<span data-ttu-id="bb93c-172">上图显示：</span><span class="sxs-lookup"><span data-stu-id="bb93c-172">The preceding chart shows:</span></span>

* <span data-ttu-id="bb93c-173">4K RPS（每秒请求）。</span><span class="sxs-lookup"><span data-stu-id="bb93c-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="bb93c-174">第 0 代 GC 集合大约每两秒发生一次。</span><span class="sxs-lookup"><span data-stu-id="bb93c-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="bb93c-175">工作集保持不变，约为 500 MB。</span><span class="sxs-lookup"><span data-stu-id="bb93c-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="bb93c-176">CPU 为 12%。</span><span class="sxs-lookup"><span data-stu-id="bb93c-176">CPU is 12%.</span></span>
* <span data-ttu-id="bb93c-177">内存消耗和释放（通过 GC）稳定。</span><span class="sxs-lookup"><span data-stu-id="bb93c-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="bb93c-178">下图以机器可以处理的最大吞吐量进行。</span><span class="sxs-lookup"><span data-stu-id="bb93c-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![前一图表](memory/_static/bigstring2.png)

<span data-ttu-id="bb93c-180">上图显示：</span><span class="sxs-lookup"><span data-stu-id="bb93c-180">The preceding chart shows:</span></span>

* <span data-ttu-id="bb93c-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="bb93c-181">22K RPS</span></span>
* <span data-ttu-id="bb93c-182">第 0 代 GC 集合每秒发生多次。</span><span class="sxs-lookup"><span data-stu-id="bb93c-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="bb93c-183">第 1 代集合被触发，因为应用每秒分配的内存显著增加。</span><span class="sxs-lookup"><span data-stu-id="bb93c-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="bb93c-184">工作集保持不变，约为 500 MB。</span><span class="sxs-lookup"><span data-stu-id="bb93c-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="bb93c-185">CPU 为 33%。</span><span class="sxs-lookup"><span data-stu-id="bb93c-185">CPU is 33%.</span></span>
* <span data-ttu-id="bb93c-186">内存消耗和释放（通过 GC）稳定。</span><span class="sxs-lookup"><span data-stu-id="bb93c-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="bb93c-187">CPU （33%）未过度使用，因此垃圾回收可以跟上大量分配。</span><span class="sxs-lookup"><span data-stu-id="bb93c-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="bb93c-188">工作站 GC 与服务器 GC</span><span class="sxs-lookup"><span data-stu-id="bb93c-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="bb93c-189">.NET 垃圾回收器有两种不同的模式：</span><span class="sxs-lookup"><span data-stu-id="bb93c-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="bb93c-190">**工作站 GC**：针对桌面进行了优化。</span><span class="sxs-lookup"><span data-stu-id="bb93c-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="bb93c-191">**服务器 GC**。</span><span class="sxs-lookup"><span data-stu-id="bb93c-191">**Server GC**.</span></span> <span data-ttu-id="bb93c-192">ASP.NET核心应用的默认 GC。</span><span class="sxs-lookup"><span data-stu-id="bb93c-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="bb93c-193">针对服务器进行了优化。</span><span class="sxs-lookup"><span data-stu-id="bb93c-193">Optimized for the server.</span></span>

<span data-ttu-id="bb93c-194">GC 模式可以在项目文件或已发布应用程序的*运行时 config.json*文件中显式设置。</span><span class="sxs-lookup"><span data-stu-id="bb93c-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="bb93c-195">以下标记显示项目文件中的`ServerGarbageCollection`设置：</span><span class="sxs-lookup"><span data-stu-id="bb93c-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="bb93c-196">在`ServerGarbageCollection`项目文件中更改需要重新生成应用。</span><span class="sxs-lookup"><span data-stu-id="bb93c-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="bb93c-197">**注：** 服务器垃圾回收在具有单个内核的计算机上**不可用**。</span><span class="sxs-lookup"><span data-stu-id="bb93c-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="bb93c-198">有关详细信息，请参阅 <xref:System.Runtime.GCSettings.IsServerGC>。</span><span class="sxs-lookup"><span data-stu-id="bb93c-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="bb93c-199">下图显示了使用工作站 GC 的 5K RPS 下的内存配置文件。</span><span class="sxs-lookup"><span data-stu-id="bb93c-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![前一图表](memory/_static/workstation.png)

<span data-ttu-id="bb93c-201">此图表和服务器版本之间的差异很大：</span><span class="sxs-lookup"><span data-stu-id="bb93c-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="bb93c-202">工作集从 500 MB 下降到 70 MB。</span><span class="sxs-lookup"><span data-stu-id="bb93c-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="bb93c-203">GC 每秒生成数次集合，而不是每两秒生成一次。</span><span class="sxs-lookup"><span data-stu-id="bb93c-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="bb93c-204">GC 从 300 MB 下降到 10 MB。</span><span class="sxs-lookup"><span data-stu-id="bb93c-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="bb93c-205">在典型的 Web 服务器环境中，CPU 使用率比内存更重要，因此服务器 GC 更好。</span><span class="sxs-lookup"><span data-stu-id="bb93c-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="bb93c-206">如果内存利用率高且 CPU 使用率相对较低，则工作站 GC 可能更具有性能。</span><span class="sxs-lookup"><span data-stu-id="bb93c-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="bb93c-207">例如，高密度托管多个内存不足的 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="bb93c-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="bb93c-208">使用 Docker 和小型容器的 GC</span><span class="sxs-lookup"><span data-stu-id="bb93c-208">GC using Docker and small containers</span></span>

<span data-ttu-id="bb93c-209">当在一台计算机上运行多个容器化应用时，工作站 GC 可能比服务器 GC 更具预制功能。</span><span class="sxs-lookup"><span data-stu-id="bb93c-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="bb93c-210">有关详细信息，请参阅[在小型容器中使用服务器 GC 运行](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/)，并在[小型容器方案第 1 部分中使用服务器 GC 运行 = GC 堆的硬限制](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)。</span><span class="sxs-lookup"><span data-stu-id="bb93c-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="bb93c-211">持久对象引用</span><span class="sxs-lookup"><span data-stu-id="bb93c-211">Persistent object references</span></span>

<span data-ttu-id="bb93c-212">GC 无法释放引用的对象。</span><span class="sxs-lookup"><span data-stu-id="bb93c-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="bb93c-213">引用但不再需要的对象会导致内存泄漏。</span><span class="sxs-lookup"><span data-stu-id="bb93c-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="bb93c-214">如果应用经常分配对象，并且在不再需要对象后无法释放它们，则内存使用量将随着时间的推移而增加。</span><span class="sxs-lookup"><span data-stu-id="bb93c-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="bb93c-215">以下 API 创建一个 10 KB 字符串实例并将其返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="bb93c-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="bb93c-216">与上一个示例的区别是此实例由静态成员引用，这意味着它永远不会可用于收集。</span><span class="sxs-lookup"><span data-stu-id="bb93c-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="bb93c-217">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="bb93c-217">The preceding code:</span></span>

* <span data-ttu-id="bb93c-218">是典型的内存泄漏的示例。</span><span class="sxs-lookup"><span data-stu-id="bb93c-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="bb93c-219">频繁调用时，会导致应用内存增加，直到进程崩溃，`OutOfMemory`出现异常。</span><span class="sxs-lookup"><span data-stu-id="bb93c-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![前一图表](memory/_static/eternal.png)

<span data-ttu-id="bb93c-221">在前面的图像中：</span><span class="sxs-lookup"><span data-stu-id="bb93c-221">In the preceding image:</span></span>

* <span data-ttu-id="bb93c-222">负载测试`/api/staticstring`终结点会导致内存线性增加。</span><span class="sxs-lookup"><span data-stu-id="bb93c-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="bb93c-223">GC 尝试通过调用第 2 代集合来释放内存，因为内存压力增大。</span><span class="sxs-lookup"><span data-stu-id="bb93c-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="bb93c-224">GC 无法释放泄漏的内存。</span><span class="sxs-lookup"><span data-stu-id="bb93c-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="bb93c-225">分配和工作集会随时间而增加。</span><span class="sxs-lookup"><span data-stu-id="bb93c-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="bb93c-226">某些方案（如缓存）要求保持对象引用，直到内存压力强制释放它们。</span><span class="sxs-lookup"><span data-stu-id="bb93c-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="bb93c-227">类<xref:System.WeakReference>可用于这种类型的缓存代码。</span><span class="sxs-lookup"><span data-stu-id="bb93c-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="bb93c-228">对象`WeakReference`是在内存压力下收集的。</span><span class="sxs-lookup"><span data-stu-id="bb93c-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="bb93c-229"><xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>使用的`WeakReference`默认实现。</span><span class="sxs-lookup"><span data-stu-id="bb93c-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="bb93c-230">本机内存</span><span class="sxs-lookup"><span data-stu-id="bb93c-230">Native memory</span></span>

<span data-ttu-id="bb93c-231">某些 .NET Core 对象依赖于本机内存。</span><span class="sxs-lookup"><span data-stu-id="bb93c-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="bb93c-232">GC**无法**收集本机内存。</span><span class="sxs-lookup"><span data-stu-id="bb93c-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="bb93c-233">使用本机内存的 .NET 对象必须使用本机代码释放它。</span><span class="sxs-lookup"><span data-stu-id="bb93c-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="bb93c-234">.NET 提供<xref:System.IDisposable>允许开发人员释放本机内存的接口。</span><span class="sxs-lookup"><span data-stu-id="bb93c-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="bb93c-235">即使<xref:System.IDisposable.Dispose*>未调用，在[终结器](/dotnet/csharp/programming-guide/classes-and-structs/destructors)运行时，也会`Dispose`正确实现类调用。</span><span class="sxs-lookup"><span data-stu-id="bb93c-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="bb93c-236">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="bb93c-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="bb93c-237">[物理文件提供程序](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0)是托管类，因此将在请求结束时收集任何实例。</span><span class="sxs-lookup"><span data-stu-id="bb93c-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="bb93c-238">下图显示连续调用 API 时的`fileprovider`内存配置文件。</span><span class="sxs-lookup"><span data-stu-id="bb93c-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![前一图表](memory/_static/fileprovider.png)

<span data-ttu-id="bb93c-240">前面的图表显示了此类实现的一个明显问题，因为它不断增加内存使用量。</span><span class="sxs-lookup"><span data-stu-id="bb93c-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="bb93c-241">这是一个已知的问题，正在跟踪[在此问题](https://github.com/dotnet/aspnetcore/issues/3110)。</span><span class="sxs-lookup"><span data-stu-id="bb93c-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="bb93c-242">用户代码中也可能发生相同的泄漏，其原因之一如下：</span><span class="sxs-lookup"><span data-stu-id="bb93c-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="bb93c-243">未正确释放类。</span><span class="sxs-lookup"><span data-stu-id="bb93c-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="bb93c-244">忘记调用应释放的`Dispose`从属对象的方法。</span><span class="sxs-lookup"><span data-stu-id="bb93c-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="bb93c-245">大型对象堆</span><span class="sxs-lookup"><span data-stu-id="bb93c-245">Large objects heap</span></span>

<span data-ttu-id="bb93c-246">频繁的内存分配/空闲周期可能会分散内存，尤其是在分配大块内存时。</span><span class="sxs-lookup"><span data-stu-id="bb93c-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="bb93c-247">对象以连续的内存块分配。</span><span class="sxs-lookup"><span data-stu-id="bb93c-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="bb93c-248">为了缓解碎片，当 GC 释放内存时，它会尝试对它进行碎片整理。</span><span class="sxs-lookup"><span data-stu-id="bb93c-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="bb93c-249">此过程称为**压缩**。</span><span class="sxs-lookup"><span data-stu-id="bb93c-249">This process is called **compaction**.</span></span> <span data-ttu-id="bb93c-250">压实涉及移动对象。</span><span class="sxs-lookup"><span data-stu-id="bb93c-250">Compaction involves moving objects.</span></span> <span data-ttu-id="bb93c-251">移动大型对象会造成性能损失。</span><span class="sxs-lookup"><span data-stu-id="bb93c-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="bb93c-252">因此，GC 会为_大型_对象（称为[大型对象堆](/dotnet/standard/garbage-collection/large-object-heap)（LOH） 创建特殊内存区域。</span><span class="sxs-lookup"><span data-stu-id="bb93c-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="bb93c-253">大于 85，000 字节（约 83 KB）的对象是：</span><span class="sxs-lookup"><span data-stu-id="bb93c-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="bb93c-254">放置在 LOH 上。</span><span class="sxs-lookup"><span data-stu-id="bb93c-254">Placed on the LOH.</span></span>
* <span data-ttu-id="bb93c-255">未压缩。</span><span class="sxs-lookup"><span data-stu-id="bb93c-255">Not compacted.</span></span>
* <span data-ttu-id="bb93c-256">在第 2 代 GC 期间收集。</span><span class="sxs-lookup"><span data-stu-id="bb93c-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="bb93c-257">当 LOH 已满时，GC 将触发第 2 代集合。</span><span class="sxs-lookup"><span data-stu-id="bb93c-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="bb93c-258">第 2 代集合：</span><span class="sxs-lookup"><span data-stu-id="bb93c-258">Generation 2 collections:</span></span>

* <span data-ttu-id="bb93c-259">本质上是缓慢的。</span><span class="sxs-lookup"><span data-stu-id="bb93c-259">Are inherently slow.</span></span>
* <span data-ttu-id="bb93c-260">此外，还要承担触发所有其他代的集合的成本。</span><span class="sxs-lookup"><span data-stu-id="bb93c-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="bb93c-261">以下代码可立即压缩 LOH：</span><span class="sxs-lookup"><span data-stu-id="bb93c-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="bb93c-262">有关<xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>压缩 LOH 的信息，请参阅。</span><span class="sxs-lookup"><span data-stu-id="bb93c-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="bb93c-263">在使用 .NET Core 3.0 及更高版本的容器中，LOH 会自动压缩。</span><span class="sxs-lookup"><span data-stu-id="bb93c-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="bb93c-264">以下说明此行为的 API：</span><span class="sxs-lookup"><span data-stu-id="bb93c-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="bb93c-265">下图显示了在最大负载下调用终结点的`/api/loh/84975`内存配置文件：</span><span class="sxs-lookup"><span data-stu-id="bb93c-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![前一图表](memory/_static/loh1.png)

<span data-ttu-id="bb93c-267">下图显示了调用终结点的`/api/loh/84976`内存配置文件，仅分配*了一个字节*：</span><span class="sxs-lookup"><span data-stu-id="bb93c-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![前一图表](memory/_static/loh2.png)

<span data-ttu-id="bb93c-269">注意：结构`byte[]`具有开销字节。</span><span class="sxs-lookup"><span data-stu-id="bb93c-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="bb93c-270">这就是为什么 84，976 字节触发 85，000 限制的原因。</span><span class="sxs-lookup"><span data-stu-id="bb93c-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="bb93c-271">比较前面的两个图表：</span><span class="sxs-lookup"><span data-stu-id="bb93c-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="bb93c-272">这两种情况的工作集都类似，大约 450 MB。</span><span class="sxs-lookup"><span data-stu-id="bb93c-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="bb93c-273">LOH 下的请求（84，975 字节）主要显示第 0 代集合。</span><span class="sxs-lookup"><span data-stu-id="bb93c-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="bb93c-274">过 LOH 请求生成恒定的第 2 代集合。</span><span class="sxs-lookup"><span data-stu-id="bb93c-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="bb93c-275">第 2 代集合成本高昂。</span><span class="sxs-lookup"><span data-stu-id="bb93c-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="bb93c-276">需要更多的 CPU，吞吐量下降近 50%。</span><span class="sxs-lookup"><span data-stu-id="bb93c-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="bb93c-277">临时大型对象特别成问题，因为它们会导致第 2 代 GC。</span><span class="sxs-lookup"><span data-stu-id="bb93c-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="bb93c-278">为了达到最佳性能，应尽量减少大型对象的使用。</span><span class="sxs-lookup"><span data-stu-id="bb93c-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="bb93c-279">如果可能，拆分大型对象。</span><span class="sxs-lookup"><span data-stu-id="bb93c-279">If possible, split up large objects.</span></span> <span data-ttu-id="bb93c-280">例如，ASP.NET Core 中的[响应缓存](xref:performance/caching/response)中间件将缓存条目拆分为小于 85，000 字节的块。</span><span class="sxs-lookup"><span data-stu-id="bb93c-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="bb93c-281">以下链接显示了将对象保持在 LOH 限制下的ASP.NET核心方法：</span><span class="sxs-lookup"><span data-stu-id="bb93c-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="bb93c-282">响应缓存/流/流实用程序。cs</span><span class="sxs-lookup"><span data-stu-id="bb93c-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="bb93c-283">响应缓存/内存响应缓存.cs</span><span class="sxs-lookup"><span data-stu-id="bb93c-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="bb93c-284">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="bb93c-284">For more information, see:</span></span>

* [<span data-ttu-id="bb93c-285">未覆盖的大对象堆</span><span class="sxs-lookup"><span data-stu-id="bb93c-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="bb93c-286">大型对象堆</span><span class="sxs-lookup"><span data-stu-id="bb93c-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="bb93c-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="bb93c-287">HttpClient</span></span>

<span data-ttu-id="bb93c-288">使用<xref:System.Net.Http.HttpClient>不当可能会导致资源泄漏。</span><span class="sxs-lookup"><span data-stu-id="bb93c-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="bb93c-289">系统资源，如数据库连接、套接字、文件句柄等：</span><span class="sxs-lookup"><span data-stu-id="bb93c-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="bb93c-290">比记忆更稀缺。</span><span class="sxs-lookup"><span data-stu-id="bb93c-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="bb93c-291">泄漏时比内存问题更大。</span><span class="sxs-lookup"><span data-stu-id="bb93c-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="bb93c-292">经验丰富的 .NET 开发人员知道<xref:System.IDisposable.Dispose*>调用实现<xref:System.IDisposable>的对象。</span><span class="sxs-lookup"><span data-stu-id="bb93c-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="bb93c-293">不释放实现`IDisposable`的对象通常会导致内存泄漏或系统资源泄漏。</span><span class="sxs-lookup"><span data-stu-id="bb93c-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="bb93c-294">`HttpClient`实现`IDisposable`，但**不应**在每一次调用时都处置。</span><span class="sxs-lookup"><span data-stu-id="bb93c-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="bb93c-295">相反，`HttpClient`应该重复使用。</span><span class="sxs-lookup"><span data-stu-id="bb93c-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="bb93c-296">以下终结点在每个请求上创建并释放一`HttpClient`个新实例：</span><span class="sxs-lookup"><span data-stu-id="bb93c-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="bb93c-297">在负载下，将记录以下错误消息：</span><span class="sxs-lookup"><span data-stu-id="bb93c-297">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="bb93c-298">即使`HttpClient`实例已释放，操作系统也会释放实际网络连接。</span><span class="sxs-lookup"><span data-stu-id="bb93c-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="bb93c-299">通过不断创建新连接，_端口耗尽_。</span><span class="sxs-lookup"><span data-stu-id="bb93c-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="bb93c-300">每个客户端连接都需要其自己的客户端端口。</span><span class="sxs-lookup"><span data-stu-id="bb93c-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="bb93c-301">防止端口耗尽的一种方法是重用同一`HttpClient`实例：</span><span class="sxs-lookup"><span data-stu-id="bb93c-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="bb93c-302">`HttpClient`当应用停止时，实例将被释放。</span><span class="sxs-lookup"><span data-stu-id="bb93c-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="bb93c-303">此示例显示，在每次使用后，不应释放每个一次性资源。</span><span class="sxs-lookup"><span data-stu-id="bb93c-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="bb93c-304">有关处理`HttpClient`实例生存期的更好方法，请参阅以下内容：</span><span class="sxs-lookup"><span data-stu-id="bb93c-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="bb93c-305">HttpClient 和生存期管理</span><span class="sxs-lookup"><span data-stu-id="bb93c-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="bb93c-306">HTTPClient 工厂博客</span><span class="sxs-lookup"><span data-stu-id="bb93c-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="bb93c-307">对象池</span><span class="sxs-lookup"><span data-stu-id="bb93c-307">Object pooling</span></span>

<span data-ttu-id="bb93c-308">前面的示例演示如何使`HttpClient`实例成为静态的，并被所有请求重用。</span><span class="sxs-lookup"><span data-stu-id="bb93c-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="bb93c-309">重用可防止资源耗尽。</span><span class="sxs-lookup"><span data-stu-id="bb93c-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="bb93c-310">对象池：</span><span class="sxs-lookup"><span data-stu-id="bb93c-310">Object pooling:</span></span>

* <span data-ttu-id="bb93c-311">使用重用模式。</span><span class="sxs-lookup"><span data-stu-id="bb93c-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="bb93c-312">专为创建成本高昂的对象而设计。</span><span class="sxs-lookup"><span data-stu-id="bb93c-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="bb93c-313">池是预初始化对象的集合，可以在线程之间保留和释放。</span><span class="sxs-lookup"><span data-stu-id="bb93c-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="bb93c-314">池可以定义分配规则，如限制、预定义大小或增长率。</span><span class="sxs-lookup"><span data-stu-id="bb93c-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="bb93c-315">NuGet 包[Microsoft.扩展.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/)包含有助于管理此类池的类。</span><span class="sxs-lookup"><span data-stu-id="bb93c-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="bb93c-316">以下 API 终结点实例化了`byte`在每个请求上填充随机数的缓冲区：</span><span class="sxs-lookup"><span data-stu-id="bb93c-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="bb93c-317">下图显示了使用中等负载调用前面的 API 的图表显示：</span><span class="sxs-lookup"><span data-stu-id="bb93c-317">The following chart display calling the preceding API with moderate load:</span></span>

![前一图表](memory/_static/array.png)

<span data-ttu-id="bb93c-319">在前面的图表中，第 0 代集合大约每秒发生一次。</span><span class="sxs-lookup"><span data-stu-id="bb93c-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="bb93c-320">可以通过使用[ArrayPool\<T>](xref:System.Buffers.ArrayPool`1)`byte`池化缓冲区来优化前面的代码。</span><span class="sxs-lookup"><span data-stu-id="bb93c-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="bb93c-321">静态实例跨请求重用。</span><span class="sxs-lookup"><span data-stu-id="bb93c-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="bb93c-322">此方法的不同做法是从 API 返回池对象。</span><span class="sxs-lookup"><span data-stu-id="bb93c-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="bb93c-323">这意味着：</span><span class="sxs-lookup"><span data-stu-id="bb93c-323">That means:</span></span>

* <span data-ttu-id="bb93c-324">从 方法返回时，对象将失去控制。</span><span class="sxs-lookup"><span data-stu-id="bb93c-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="bb93c-325">无法释放对象。</span><span class="sxs-lookup"><span data-stu-id="bb93c-325">You can't release the object.</span></span>

<span data-ttu-id="bb93c-326">要设置对象的处置：</span><span class="sxs-lookup"><span data-stu-id="bb93c-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="bb93c-327">将池数组封装在一次性对象中。</span><span class="sxs-lookup"><span data-stu-id="bb93c-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="bb93c-328">使用[HttpContext.Response.注册为 Dispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)注册池对象。</span><span class="sxs-lookup"><span data-stu-id="bb93c-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="bb93c-329">`RegisterForDispose`将负责对目标对象的`Dispose`调用，以便仅在 HTTP 请求完成时释放它。</span><span class="sxs-lookup"><span data-stu-id="bb93c-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="bb93c-330">应用与非池式版本相同的负载会导致以下图表：</span><span class="sxs-lookup"><span data-stu-id="bb93c-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![前一图表](memory/_static/pooledarray.png)

<span data-ttu-id="bb93c-332">主要区别是分配字节，因此，第 0 代集合要少得多。</span><span class="sxs-lookup"><span data-stu-id="bb93c-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bb93c-333">其他资源</span><span class="sxs-lookup"><span data-stu-id="bb93c-333">Additional resources</span></span>

* [<span data-ttu-id="bb93c-334">垃圾回收</span><span class="sxs-lookup"><span data-stu-id="bb93c-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="bb93c-335">使用并发可视化工具了解不同的 GC 模式</span><span class="sxs-lookup"><span data-stu-id="bb93c-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="bb93c-336">未覆盖的大对象堆</span><span class="sxs-lookup"><span data-stu-id="bb93c-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="bb93c-337">大型对象堆</span><span class="sxs-lookup"><span data-stu-id="bb93c-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
