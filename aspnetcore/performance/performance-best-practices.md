---
title: ASP.NET Core 性能最佳做法
author: mjrousos
description: 在 ASP.NET Core 应用中提高性能并避免出现常见性能问题的提示。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
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
uid: performance/performance-best-practices
ms.openlocfilehash: 587872b269d897d7c86eb77c110a4b6432218ed3
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746554"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="5988b-103">ASP.NET Core 性能最佳做法</span><span class="sxs-lookup"><span data-stu-id="5988b-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="5988b-104">作者：[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="5988b-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="5988b-105">本文提供了有关 ASP.NET Core 的性能最佳做法的准则。</span><span class="sxs-lookup"><span data-stu-id="5988b-105">This article provides guidelines for performance best practices with ASP.NET Core.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="5988b-106">主动缓存</span><span class="sxs-lookup"><span data-stu-id="5988b-106">Cache aggressively</span></span>

<span data-ttu-id="5988b-107">此文档的几个部分讨论了缓存。</span><span class="sxs-lookup"><span data-stu-id="5988b-107">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="5988b-108">有关详细信息，请参阅 <xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="5988b-108">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="understand-hot-code-paths"></a><span data-ttu-id="5988b-109">了解热代码路径</span><span class="sxs-lookup"><span data-stu-id="5988b-109">Understand hot code paths</span></span>

<span data-ttu-id="5988b-110">在本文档中，将 *热代码路径* 定义为经常调用的代码路径和执行时间量。</span><span class="sxs-lookup"><span data-stu-id="5988b-110">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="5988b-111">热代码路径通常限制应用向外缩放和性能，并将在本文档的几个部分中进行讨论。</span><span class="sxs-lookup"><span data-stu-id="5988b-111">Hot code paths typically limit app scale-out and performance and are discussed in several parts of this document.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="5988b-112">避免阻止调用</span><span class="sxs-lookup"><span data-stu-id="5988b-112">Avoid blocking calls</span></span>

<span data-ttu-id="5988b-113">应将 ASP.NET Core 应用程序设计为同时处理许多请求。</span><span class="sxs-lookup"><span data-stu-id="5988b-113">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="5988b-114">异步 Api 允许一小部分线程通过不等待阻止调用来处理上千个并发请求。</span><span class="sxs-lookup"><span data-stu-id="5988b-114">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="5988b-115">线程可以处理另一请求，而不是等待长时间运行的同步任务完成。</span><span class="sxs-lookup"><span data-stu-id="5988b-115">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="5988b-116">ASP.NET Core 应用中的常见性能问题是阻止可能是异步的调用。</span><span class="sxs-lookup"><span data-stu-id="5988b-116">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="5988b-117">很多同步阻塞调用会导致 [线程池](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) 不足并降低响应时间。</span><span class="sxs-lookup"><span data-stu-id="5988b-117">Many synchronous blocking calls lead to [Thread Pool starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) and degraded response times.</span></span>

<span data-ttu-id="5988b-118">**请勿**：</span><span class="sxs-lookup"><span data-stu-id="5988b-118">**Do not**:</span></span>

* <span data-ttu-id="5988b-119">通过调用 [task. Wait](/dotnet/api/system.threading.tasks.task.wait) 或 [task.](/dotnet/api/system.threading.tasks.task-1.result)来阻止异步执行。</span><span class="sxs-lookup"><span data-stu-id="5988b-119">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="5988b-120">获取通用代码路径中的锁。</span><span class="sxs-lookup"><span data-stu-id="5988b-120">Acquire locks in common code paths.</span></span> <span data-ttu-id="5988b-121">当构建为并行运行代码时，ASP.NET Core 应用程序的性能最高。</span><span class="sxs-lookup"><span data-stu-id="5988b-121">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>
* <span data-ttu-id="5988b-122">调用 [任务。运行](/dotnet/api/system.threading.tasks.task.run) 并立即等待。</span><span class="sxs-lookup"><span data-stu-id="5988b-122">Call [Task.Run](/dotnet/api/system.threading.tasks.task.run) and immediately await it.</span></span> <span data-ttu-id="5988b-123">ASP.NET Core 已在正常线程池线程上运行应用程序代码，因此调用任务。运行仅会导致额外的不必要的线程池计划。</span><span class="sxs-lookup"><span data-stu-id="5988b-123">ASP.NET Core already runs app code on normal Thread Pool threads, so calling Task.Run only results in extra unnecessary Thread Pool scheduling.</span></span> <span data-ttu-id="5988b-124">即使计划的代码会阻止线程，任务也不会阻止。</span><span class="sxs-lookup"><span data-stu-id="5988b-124">Even if the scheduled code would block a thread, Task.Run does not prevent that.</span></span>

<span data-ttu-id="5988b-125">**Do**：</span><span class="sxs-lookup"><span data-stu-id="5988b-125">**Do**:</span></span>

* <span data-ttu-id="5988b-126">使 [热代码路径](#understand-hot-code-paths) 处于异步状态。</span><span class="sxs-lookup"><span data-stu-id="5988b-126">Make [hot code paths](#understand-hot-code-paths) asynchronous.</span></span>
* <span data-ttu-id="5988b-127">如果异步 API 可用，则异步调用数据访问、i/o 和长时间运行的操作 Api。</span><span class="sxs-lookup"><span data-stu-id="5988b-127">Call data access, I/O, and long-running operations APIs asynchronously if an asynchronous API is available.</span></span> <span data-ttu-id="5988b-128">不要**使用**[任务。运行](/dotnet/api/system.threading.tasks.task.run)以使同步 API 成为异步同步。</span><span class="sxs-lookup"><span data-stu-id="5988b-128">Do **not** use [Task.Run](/dotnet/api/system.threading.tasks.task.run) to make a synchronous API asynchronous.</span></span>
* <span data-ttu-id="5988b-129">使控制器/ Razor 页面操作异步。</span><span class="sxs-lookup"><span data-stu-id="5988b-129">Make controller/Razor Page actions asynchronous.</span></span> <span data-ttu-id="5988b-130">为了受益于 [async/await](/dotnet/csharp/programming-guide/concepts/async/) 模式，整个调用堆栈是异步的。</span><span class="sxs-lookup"><span data-stu-id="5988b-130">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="5988b-131">探查器（如 [PerfView](https://github.com/Microsoft/perfview)）可用于查找频繁添加到 [线程池中](/windows/desktop/procthread/thread-pools)的线程。</span><span class="sxs-lookup"><span data-stu-id="5988b-131">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="5988b-132">`Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`事件指示添加到线程池的线程。</span><span class="sxs-lookup"><span data-stu-id="5988b-132">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="5988b-133">返回 IEnumerable \<T> 或 IAsyncEnumerable\<T></span><span class="sxs-lookup"><span data-stu-id="5988b-133">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="5988b-134">`IEnumerable<T>`从操作返回会导致序列化程序同步集合迭代。</span><span class="sxs-lookup"><span data-stu-id="5988b-134">Returning `IEnumerable<T>` from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="5988b-135">因此会阻止调用，并且可能会导致线程池资源不足。</span><span class="sxs-lookup"><span data-stu-id="5988b-135">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="5988b-136">若要避免同步枚举，请 `ToListAsync` 在返回可枚举的前使用。</span><span class="sxs-lookup"><span data-stu-id="5988b-136">To avoid synchronous enumeration, use `ToListAsync` before returning the enumerable.</span></span>

<span data-ttu-id="5988b-137">从 ASP.NET Core 3.0 开始， `IAsyncEnumerable<T>` 可将其用作 `IEnumerable<T>` 异步枚举的替代方法。</span><span class="sxs-lookup"><span data-stu-id="5988b-137">Beginning with ASP.NET Core 3.0, `IAsyncEnumerable<T>` can be used as an alternative to `IEnumerable<T>` that enumerates asynchronously.</span></span> <span data-ttu-id="5988b-138">有关详细信息，请参阅 [控制器操作返回类型](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet)。</span><span class="sxs-lookup"><span data-stu-id="5988b-138">For more information, see [Controller action return types](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet).</span></span>

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="5988b-139">最小化大型对象分配</span><span class="sxs-lookup"><span data-stu-id="5988b-139">Minimize large object allocations</span></span>

<span data-ttu-id="5988b-140">[.Net Core 垃圾回收器](/dotnet/standard/garbage-collection/)在 ASP.NET Core 应用中自动管理内存的分配和释放。</span><span class="sxs-lookup"><span data-stu-id="5988b-140">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="5988b-141">自动垃圾回收通常意味着开发人员无需担心如何或何时释放内存。</span><span class="sxs-lookup"><span data-stu-id="5988b-141">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="5988b-142">但是，清理未引用的对象会占用 CPU 时间，因此开发人员应最大限度地减少 [热代码路径](#understand-hot-code-paths)中的对象分配。</span><span class="sxs-lookup"><span data-stu-id="5988b-142">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#understand-hot-code-paths).</span></span> <span data-ttu-id="5988b-143">垃圾回收在大型对象上特别昂贵 ( # A0 85 K 字节) 。</span><span class="sxs-lookup"><span data-stu-id="5988b-143">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="5988b-144">大型对象存储在 [大型对象堆](/dotnet/standard/garbage-collection/large-object-heap) 上，需要完整的 (第2代) 垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="5988b-144">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="5988b-145">与第0代和第1代回收不同，第2代回收需要临时暂停应用执行。</span><span class="sxs-lookup"><span data-stu-id="5988b-145">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="5988b-146">频繁分配和取消分配大型对象会导致性能不一致。</span><span class="sxs-lookup"><span data-stu-id="5988b-146">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="5988b-147">建议：</span><span class="sxs-lookup"><span data-stu-id="5988b-147">Recommendations:</span></span>

* <span data-ttu-id="5988b-148">**请考虑缓存** 经常使用的大型对象。</span><span class="sxs-lookup"><span data-stu-id="5988b-148">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="5988b-149">缓存大型对象会阻止开销较高的分配。</span><span class="sxs-lookup"><span data-stu-id="5988b-149">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="5988b-150">使用[ArrayPool \<T> ](/dotnet/api/system.buffers.arraypool-1)存储大型数组**来池缓冲区**。</span><span class="sxs-lookup"><span data-stu-id="5988b-150">**Do** pool buffers by using an [ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="5988b-151">**不要** 在 [热代码路径](#understand-hot-code-paths)上分配很多生存期较短的大型对象。</span><span class="sxs-lookup"><span data-stu-id="5988b-151">**Do not** allocate many, short-lived large objects on [hot code paths](#understand-hot-code-paths).</span></span>

<span data-ttu-id="5988b-152">可以通过查看 [PerfView](https://github.com/Microsoft/perfview) 中的垃圾回收 (GC) 统计信息并进行检查来诊断内存问题，如前面的问题：</span><span class="sxs-lookup"><span data-stu-id="5988b-152">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="5988b-153">垃圾回收暂停时间。</span><span class="sxs-lookup"><span data-stu-id="5988b-153">Garbage collection pause time.</span></span>
* <span data-ttu-id="5988b-154">垃圾回收所用的处理器时间百分比。</span><span class="sxs-lookup"><span data-stu-id="5988b-154">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="5988b-155">第0代、第1代和第2代垃圾回收量。</span><span class="sxs-lookup"><span data-stu-id="5988b-155">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="5988b-156">有关详细信息，请参阅 [垃圾回收和性能](/dotnet/standard/garbage-collection/performance)。</span><span class="sxs-lookup"><span data-stu-id="5988b-156">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access-and-io"></a><span data-ttu-id="5988b-157">优化数据访问和 i/o</span><span class="sxs-lookup"><span data-stu-id="5988b-157">Optimize data access and I/O</span></span>

<span data-ttu-id="5988b-158">与数据存储和其他远程服务的交互通常是 ASP.NET Core 应用程序的最慢部分。</span><span class="sxs-lookup"><span data-stu-id="5988b-158">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="5988b-159">有效读取和写入数据对于良好的性能至关重要。</span><span class="sxs-lookup"><span data-stu-id="5988b-159">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="5988b-160">建议：</span><span class="sxs-lookup"><span data-stu-id="5988b-160">Recommendations:</span></span>

* <span data-ttu-id="5988b-161">**请** 以异步方式调用所有数据访问 api。</span><span class="sxs-lookup"><span data-stu-id="5988b-161">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="5988b-162">检索的数据**不**是必需的。</span><span class="sxs-lookup"><span data-stu-id="5988b-162">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="5988b-163">编写查询以仅返回当前 HTTP 请求所必需的数据。</span><span class="sxs-lookup"><span data-stu-id="5988b-163">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="5988b-164">如果数据可以接受，**请考虑缓存**经常访问的从数据库或远程服务检索的数据。</span><span class="sxs-lookup"><span data-stu-id="5988b-164">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="5988b-165">使用 [MemoryCache](xref:performance/caching/memory) 或 [microsoft.web.distributedcache](xref:performance/caching/distributed)，具体取决于方案。</span><span class="sxs-lookup"><span data-stu-id="5988b-165">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="5988b-166">有关详细信息，请参阅 <xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="5988b-166">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="5988b-167">**尽量减少** 网络往返次数。</span><span class="sxs-lookup"><span data-stu-id="5988b-167">**Do** minimize network round trips.</span></span> <span data-ttu-id="5988b-168">目标是使用单个调用而不是多个调用来检索所需数据。</span><span class="sxs-lookup"><span data-stu-id="5988b-168">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="5988b-169">在访问数据时，**请不要**在 Entity Framework Core 中使用[无跟踪查询](/ef/core/querying/tracking#no-tracking-queries)。</span><span class="sxs-lookup"><span data-stu-id="5988b-169">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="5988b-170">EF Core 可以更有效地返回无跟踪查询的结果。</span><span class="sxs-lookup"><span data-stu-id="5988b-170">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="5988b-171">使用、或语句\*\* (筛选和\*\*聚合 LINQ 查询 `.Where` `.Select` `.Sum` ，例如) ，以便数据库执行筛选。</span><span class="sxs-lookup"><span data-stu-id="5988b-171">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="5988b-172">**请考虑 EF Core** 在客户端上解析一些查询运算符，这可能导致查询执行效率低下。</span><span class="sxs-lookup"><span data-stu-id="5988b-172">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="5988b-173">有关详细信息，请参阅 [客户端评估性能问题](/ef/core/querying/client-eval#client-evaluation-performance-issues)。</span><span class="sxs-lookup"><span data-stu-id="5988b-173">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="5988b-174">**不要** 对集合使用投影查询，这可能会导致执行 "N + 1" 个 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="5988b-174">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="5988b-175">有关详细信息，请参阅 [相关子查询的优化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)。</span><span class="sxs-lookup"><span data-stu-id="5988b-175">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="5988b-176">请参阅 [EF 高性能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) ，了解可提高大规模应用程序性能的方法：</span><span class="sxs-lookup"><span data-stu-id="5988b-176">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="5988b-177">DbContext 池</span><span class="sxs-lookup"><span data-stu-id="5988b-177">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="5988b-178">显式编译的查询</span><span class="sxs-lookup"><span data-stu-id="5988b-178">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="5988b-179">建议在提交基本代码之前测量前面的高性能方法的影响。</span><span class="sxs-lookup"><span data-stu-id="5988b-179">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="5988b-180">已编译查询的额外复杂性可能不会提高性能。</span><span class="sxs-lookup"><span data-stu-id="5988b-180">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="5988b-181">通过查看 [Application Insights](/azure/application-insights/app-insights-overview) 或分析工具访问数据所用的时间，可以检测到查询问题。</span><span class="sxs-lookup"><span data-stu-id="5988b-181">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="5988b-182">大多数数据库还提供有关频繁执行的查询的统计信息。</span><span class="sxs-lookup"><span data-stu-id="5988b-182">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="5988b-183">与 HttpClientFactory 建立池 HTTP 连接</span><span class="sxs-lookup"><span data-stu-id="5988b-183">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="5988b-184">尽管 [HttpClient](/dotnet/api/system.net.http.httpclient) 实现了 `IDisposable` 接口，但它是为重复使用而设计的。</span><span class="sxs-lookup"><span data-stu-id="5988b-184">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="5988b-185">关闭 `HttpClient` 的实例使套接字在 `TIME_WAIT` 一小段时间内处于打开状态。</span><span class="sxs-lookup"><span data-stu-id="5988b-185">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="5988b-186">如果经常使用创建和处置对象的代码路径 `HttpClient` ，应用可能会耗尽可用的套接字。</span><span class="sxs-lookup"><span data-stu-id="5988b-186">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="5988b-187">ASP.NET Core 2.1 中引入了[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)作为此问题的解决方案。</span><span class="sxs-lookup"><span data-stu-id="5988b-187">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="5988b-188">它处理池 HTTP 连接以优化性能和可靠性。</span><span class="sxs-lookup"><span data-stu-id="5988b-188">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="5988b-189">建议：</span><span class="sxs-lookup"><span data-stu-id="5988b-189">Recommendations:</span></span>

* <span data-ttu-id="5988b-190">**不要** 直接创建和释放 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="5988b-190">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="5988b-191">**请** 使用 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) 来检索 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="5988b-191">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="5988b-192">有关详细信息，请参阅[使用 HttpClientFactory 实现可复原的 HTTP 请求](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)。</span><span class="sxs-lookup"><span data-stu-id="5988b-192">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="5988b-193">快速保持通用代码路径</span><span class="sxs-lookup"><span data-stu-id="5988b-193">Keep common code paths fast</span></span>

<span data-ttu-id="5988b-194">您希望所有代码的速度都很快。</span><span class="sxs-lookup"><span data-stu-id="5988b-194">You want all of your code to be fast.</span></span> <span data-ttu-id="5988b-195">经常称为 "代码路径" 是最重要的。</span><span class="sxs-lookup"><span data-stu-id="5988b-195">Frequently-called code paths are the most critical to optimize.</span></span> <span data-ttu-id="5988b-196">这些方法包括：</span><span class="sxs-lookup"><span data-stu-id="5988b-196">These include:</span></span>

* <span data-ttu-id="5988b-197">应用程序的请求处理管道中的中间件组件，尤其是在管道早期运行的中间件。</span><span class="sxs-lookup"><span data-stu-id="5988b-197">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="5988b-198">这些组件会对性能产生很大的影响。</span><span class="sxs-lookup"><span data-stu-id="5988b-198">These components have a large impact on performance.</span></span>
* <span data-ttu-id="5988b-199">针对每个请求或每个请求多次执行的代码。</span><span class="sxs-lookup"><span data-stu-id="5988b-199">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="5988b-200">例如，自定义日志记录、授权处理程序或暂时性服务的初始化。</span><span class="sxs-lookup"><span data-stu-id="5988b-200">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="5988b-201">建议：</span><span class="sxs-lookup"><span data-stu-id="5988b-201">Recommendations:</span></span>

* <span data-ttu-id="5988b-202">**不要** 将自定义中间件组件用于长时间运行的任务。</span><span class="sxs-lookup"><span data-stu-id="5988b-202">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="5988b-203">**请使用性能** 分析工具（如 [Visual Studio 诊断工具](/visualstudio/profiling/profiling-feature-tour) 或 [PerfView](https://github.com/Microsoft/perfview)) ）来识别 [热代码路径](#understand-hot-code-paths)。</span><span class="sxs-lookup"><span data-stu-id="5988b-203">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#understand-hot-code-paths).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="5988b-204">在 HTTP 请求之外完成长时间运行的任务</span><span class="sxs-lookup"><span data-stu-id="5988b-204">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="5988b-205">大多数对 ASP.NET Core 应用程序的请求都可以通过控制器或页面模型进行处理，该模型调用必要的服务并返回 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="5988b-205">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="5988b-206">对于涉及长时间运行的任务的某些请求，最好将整个请求响应过程设为异步处理。</span><span class="sxs-lookup"><span data-stu-id="5988b-206">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="5988b-207">建议：</span><span class="sxs-lookup"><span data-stu-id="5988b-207">Recommendations:</span></span>

* <span data-ttu-id="5988b-208">**请** 不要等待长时间运行的任务在普通的 HTTP 请求处理过程中完成。</span><span class="sxs-lookup"><span data-stu-id="5988b-208">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="5988b-209">**请考虑使用**[后台服务](xref:fundamentals/host/hosted-services)处理长时间运行的请求，或使用[Azure 函数](/azure/azure-functions/)处理进程外的请求。</span><span class="sxs-lookup"><span data-stu-id="5988b-209">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="5988b-210">在进程外完成工作对于 CPU 密集型任务特别有用。</span><span class="sxs-lookup"><span data-stu-id="5988b-210">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="5988b-211">**请使用实时** 通信选项（如 [SignalR](xref:signalr/introduction) ）以异步方式与客户端进行通信。</span><span class="sxs-lookup"><span data-stu-id="5988b-211">**Do** use real-time communication options, such as [SignalR](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="5988b-212">缩小客户端资产</span><span class="sxs-lookup"><span data-stu-id="5988b-212">Minify client assets</span></span>

<span data-ttu-id="5988b-213">具有复杂前端的 ASP.NET Core 应用通常会提供许多 JavaScript、CSS 或图像文件。</span><span class="sxs-lookup"><span data-stu-id="5988b-213">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="5988b-214">可以通过以下方式改善初始负载请求的性能：</span><span class="sxs-lookup"><span data-stu-id="5988b-214">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="5988b-215">绑定，将多个文件合并到一个文件中。</span><span class="sxs-lookup"><span data-stu-id="5988b-215">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="5988b-216">缩小，它通过删除空白和注释来减小文件大小。</span><span class="sxs-lookup"><span data-stu-id="5988b-216">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="5988b-217">建议：</span><span class="sxs-lookup"><span data-stu-id="5988b-217">Recommendations:</span></span>

* <span data-ttu-id="5988b-218">**请** 使用 ASP.NET Core 的 [内置支持](xref:client-side/bundling-and-minification) ，以便对客户端资产进行捆绑和缩小。</span><span class="sxs-lookup"><span data-stu-id="5988b-218">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="5988b-219">**请考虑其他** 第三方工具（如 [Webpack](https://webpack.js.org/)），以实现复杂的客户端资产管理。</span><span class="sxs-lookup"><span data-stu-id="5988b-219">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="5988b-220">压缩响应</span><span class="sxs-lookup"><span data-stu-id="5988b-220">Compress responses</span></span>

 <span data-ttu-id="5988b-221">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="5988b-221">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="5988b-222">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="5988b-222">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="5988b-223">有关详细信息，请参阅 [响应压缩](xref:performance/response-compression)。</span><span class="sxs-lookup"><span data-stu-id="5988b-223">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="5988b-224">使用最新 ASP.NET Core 版本</span><span class="sxs-lookup"><span data-stu-id="5988b-224">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="5988b-225">ASP.NET Core 的每个新版本都包括性能改进。</span><span class="sxs-lookup"><span data-stu-id="5988b-225">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="5988b-226">.NET Core 和 ASP.NET Core 中的优化意味着较新版本通常优于较旧的版本。</span><span class="sxs-lookup"><span data-stu-id="5988b-226">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="5988b-227">例如，.NET Core 2.1 添加了对[范围 \<T> ](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay)内已编译的正则表达式和获益的支持。</span><span class="sxs-lookup"><span data-stu-id="5988b-227">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [Span\<T>](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay).</span></span> <span data-ttu-id="5988b-228">ASP.NET Core 2.2 添加了对 HTTP/2 的支持。</span><span class="sxs-lookup"><span data-stu-id="5988b-228">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="5988b-229">[ASP.NET Core 3.0 添加了许多改进](xref:aspnetcore-3.0) ，减少了内存使用量并提高了吞吐量。</span><span class="sxs-lookup"><span data-stu-id="5988b-229">[ASP.NET Core 3.0 adds many improvements](xref:aspnetcore-3.0) that reduce memory usage and improve throughput.</span></span> <span data-ttu-id="5988b-230">如果性能是优先考虑的，请考虑升级到 ASP.NET Core 的当前版本。</span><span class="sxs-lookup"><span data-stu-id="5988b-230">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

## <a name="minimize-exceptions"></a><span data-ttu-id="5988b-231">最小化异常</span><span class="sxs-lookup"><span data-stu-id="5988b-231">Minimize exceptions</span></span>

<span data-ttu-id="5988b-232">异常应极少。</span><span class="sxs-lookup"><span data-stu-id="5988b-232">Exceptions should be rare.</span></span> <span data-ttu-id="5988b-233">相对于其他代码流模式，引发和捕获异常的速度很慢。</span><span class="sxs-lookup"><span data-stu-id="5988b-233">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="5988b-234">因此，不应使用异常来控制正常的程序流。</span><span class="sxs-lookup"><span data-stu-id="5988b-234">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="5988b-235">建议：</span><span class="sxs-lookup"><span data-stu-id="5988b-235">Recommendations:</span></span>

* <span data-ttu-id="5988b-236">**不要** 使用引发或捕获异常作为正常程序流的方法，尤其是在 [热代码路径](#understand-hot-code-paths)中。</span><span class="sxs-lookup"><span data-stu-id="5988b-236">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#understand-hot-code-paths).</span></span>
* <span data-ttu-id="5988b-237">在应用程序**中包括逻辑**，以检测和处理会导致异常的情况。</span><span class="sxs-lookup"><span data-stu-id="5988b-237">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="5988b-238">**引发或** 捕获异常或意外情况的异常。</span><span class="sxs-lookup"><span data-stu-id="5988b-238">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="5988b-239">应用诊断工具（如 Application Insights）可帮助识别应用中可能影响性能的常见异常。</span><span class="sxs-lookup"><span data-stu-id="5988b-239">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>

## <a name="performance-and-reliability"></a><span data-ttu-id="5988b-240">性能和可靠性</span><span class="sxs-lookup"><span data-stu-id="5988b-240">Performance and reliability</span></span>

<span data-ttu-id="5988b-241">以下各节提供了性能提示以及已知的可靠性问题和解决方案。</span><span class="sxs-lookup"><span data-stu-id="5988b-241">The following sections provide performance tips and known reliability problems and solutions.</span></span>

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a><span data-ttu-id="5988b-242">避免 HttpRequest/Httpresponse.cache 正文上的同步读取或写入</span><span class="sxs-lookup"><span data-stu-id="5988b-242">Avoid synchronous read or write on HttpRequest/HttpResponse body</span></span>

<span data-ttu-id="5988b-243">ASP.NET Core 中的所有 i/o 都是异步的。</span><span class="sxs-lookup"><span data-stu-id="5988b-243">All I/O in ASP.NET Core is asynchronous.</span></span> <span data-ttu-id="5988b-244">服务器实现 `Stream` 了接口，该接口同时具有同步和异步重载。</span><span class="sxs-lookup"><span data-stu-id="5988b-244">Servers implement the `Stream` interface, which has both synchronous and asynchronous overloads.</span></span> <span data-ttu-id="5988b-245">应首选异步文件以避免阻塞线程池线程。</span><span class="sxs-lookup"><span data-stu-id="5988b-245">The asynchronous ones should be preferred to avoid blocking thread pool threads.</span></span> <span data-ttu-id="5988b-246">阻塞线程可能会导致线程池不足。</span><span class="sxs-lookup"><span data-stu-id="5988b-246">Blocking threads can lead to thread pool starvation.</span></span>

<span data-ttu-id="5988b-247">请勿**执行此操作：** 下面的示例使用 <xref:System.IO.StreamReader.ReadToEnd*> 。</span><span class="sxs-lookup"><span data-stu-id="5988b-247">**Do not do this:** The following example uses the <xref:System.IO.StreamReader.ReadToEnd*>.</span></span> <span data-ttu-id="5988b-248">此方法阻止当前线程等待结果。</span><span class="sxs-lookup"><span data-stu-id="5988b-248">It blocks the current thread to wait for the result.</span></span> <span data-ttu-id="5988b-249">这是一个 [通过异步同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)的示例。</span><span class="sxs-lookup"><span data-stu-id="5988b-249">This is an example of [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

<span data-ttu-id="5988b-250">在上面的代码中， `Get` 将整个 HTTP 请求正文以同步方式读入内存中。</span><span class="sxs-lookup"><span data-stu-id="5988b-250">In the preceding code, `Get` synchronously reads the entire HTTP request body into memory.</span></span> <span data-ttu-id="5988b-251">如果客户端缓慢上传，则应用通过异步执行同步。</span><span class="sxs-lookup"><span data-stu-id="5988b-251">If the client is slowly uploading, the app is doing sync over async.</span></span> <span data-ttu-id="5988b-252">应用通过异步同步，因为 Kestrel **不支持同步** 读取。</span><span class="sxs-lookup"><span data-stu-id="5988b-252">The app does sync over async because Kestrel does **NOT** support synchronous reads.</span></span>

<span data-ttu-id="5988b-253">**执行以下操作：** 下面的示例使用 <xref:System.IO.StreamReader.ReadToEndAsync*> ，在读取时不会阻止线程。</span><span class="sxs-lookup"><span data-stu-id="5988b-253">**Do this:** The following example uses <xref:System.IO.StreamReader.ReadToEndAsync*> and does not block the thread while reading.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

<span data-ttu-id="5988b-254">前面的代码异步将整个 HTTP 请求正文读入内存中。</span><span class="sxs-lookup"><span data-stu-id="5988b-254">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="5988b-255">如果请求很大，则将整个 HTTP 请求正文读取到内存中可能会导致内存不足 (OOM) 情况。</span><span class="sxs-lookup"><span data-stu-id="5988b-255">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="5988b-256">OOM 可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="5988b-256">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="5988b-257">有关详细信息，请参阅本文档中的 [避免将大型请求正文或响应正文读入内存](#arlb) 中。</span><span class="sxs-lookup"><span data-stu-id="5988b-257">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="5988b-258">**执行以下操作：** 下面的示例使用非缓冲请求正文完全异步：</span><span class="sxs-lookup"><span data-stu-id="5988b-258">**Do this:** The following example is fully asynchronous using a non buffered request body:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

<span data-ttu-id="5988b-259">前面的代码将请求正文异步反序列化为 c # 对象。</span><span class="sxs-lookup"><span data-stu-id="5988b-259">The preceding code asynchronously de-serializes the request body into a C# object.</span></span>

## <a name="prefer-readformasync-over-requestform"></a><span data-ttu-id="5988b-260">首选 ReadFormAsync over 请求。窗体</span><span class="sxs-lookup"><span data-stu-id="5988b-260">Prefer ReadFormAsync over Request.Form</span></span>

<span data-ttu-id="5988b-261">请使用 `HttpContext.Request.ReadFormAsync`，而不是 `HttpContext.Request.Form`。</span><span class="sxs-lookup"><span data-stu-id="5988b-261">Use `HttpContext.Request.ReadFormAsync` instead of `HttpContext.Request.Form`.</span></span>
<span data-ttu-id="5988b-262">`HttpContext.Request.Form` 只能在以下条件下安全地读取：</span><span class="sxs-lookup"><span data-stu-id="5988b-262">`HttpContext.Request.Form` can be safely read only with the following conditions:</span></span>

* <span data-ttu-id="5988b-263">已通过对的调用读取了窗体 `ReadFormAsync` ，</span><span class="sxs-lookup"><span data-stu-id="5988b-263">The form has been read by a call to `ReadFormAsync`, and</span></span>
* <span data-ttu-id="5988b-264">正在使用读取缓存的表单值 `HttpContext.Request.Form`</span><span class="sxs-lookup"><span data-stu-id="5988b-264">The cached form value is being read using `HttpContext.Request.Form`</span></span>

<span data-ttu-id="5988b-265">请勿**执行此操作：** 下面的示例使用 `HttpContext.Request.Form` 。</span><span class="sxs-lookup"><span data-stu-id="5988b-265">**Do not do this:** The following example uses `HttpContext.Request.Form`.</span></span>  <span data-ttu-id="5988b-266">`HttpContext.Request.Form`[通过异步使用同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)，并可能导致线程池不足。</span><span class="sxs-lookup"><span data-stu-id="5988b-266">`HttpContext.Request.Form` uses [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) and can lead to thread pool starvation.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

<span data-ttu-id="5988b-267">**执行以下操作：** 下面的示例使用 `HttpContext.Request.ReadFormAsync` 以异步方式读取窗体体。</span><span class="sxs-lookup"><span data-stu-id="5988b-267">**Do this:** The following example uses `HttpContext.Request.ReadFormAsync` to read the form body asynchronously.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a><span data-ttu-id="5988b-268">避免将大型请求正文或响应正文读入内存</span><span class="sxs-lookup"><span data-stu-id="5988b-268">Avoid reading large request bodies or response bodies into memory</span></span>

<span data-ttu-id="5988b-269">在 .NET 中，大于 85 KB 的每个对象分配将在大型对象堆 ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) 结束。</span><span class="sxs-lookup"><span data-stu-id="5988b-269">In .NET, every object allocation greater than 85 KB ends up in the large object heap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)).</span></span> <span data-ttu-id="5988b-270">大型对象的开销很大：</span><span class="sxs-lookup"><span data-stu-id="5988b-270">Large objects are expensive in two ways:</span></span>

* <span data-ttu-id="5988b-271">分配开销较高，因为必须清除新分配的大型对象的内存。</span><span class="sxs-lookup"><span data-stu-id="5988b-271">The allocation cost is high because the memory for a newly allocated large object has to be cleared.</span></span> <span data-ttu-id="5988b-272">CLR 确保清除所有新分配对象的内存。</span><span class="sxs-lookup"><span data-stu-id="5988b-272">The CLR guarantees that memory for all newly allocated objects is cleared.</span></span>
* <span data-ttu-id="5988b-273">LOH 随堆的其余部分一起收集。</span><span class="sxs-lookup"><span data-stu-id="5988b-273">LOH is collected with the rest of the heap.</span></span> <span data-ttu-id="5988b-274">LOH 需要完整的 [垃圾回收](/dotnet/standard/garbage-collection/fundamentals) 或 [Gen2 集合](/dotnet/standard/garbage-collection/fundamentals#generations)。</span><span class="sxs-lookup"><span data-stu-id="5988b-274">LOH requires a full [garbage collection](/dotnet/standard/garbage-collection/fundamentals) or [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations).</span></span>

<span data-ttu-id="5988b-275">此 [博客文章](https://adamsitnik.com/Array-Pool/#the-problem) 简单介绍了问题：</span><span class="sxs-lookup"><span data-stu-id="5988b-275">This [blog post](https://adamsitnik.com/Array-Pool/#the-problem) describes the problem succinctly:</span></span>

> <span data-ttu-id="5988b-276">分配大型对象时，会将其标记为第2代对象。</span><span class="sxs-lookup"><span data-stu-id="5988b-276">When a large object is allocated, it's marked as Gen 2 object.</span></span> <span data-ttu-id="5988b-277">对于小对象，不是0代。</span><span class="sxs-lookup"><span data-stu-id="5988b-277">Not Gen 0 as for small objects.</span></span> <span data-ttu-id="5988b-278">后果是，如果在 LOH 中用尽内存，GC 将清除整个托管堆，而不仅是 LOH。</span><span class="sxs-lookup"><span data-stu-id="5988b-278">The consequences are that if you run out of memory in LOH, GC cleans up the whole managed heap, not only LOH.</span></span> <span data-ttu-id="5988b-279">因此，它会清除第0代第1代和第2代，包括 LOH。</span><span class="sxs-lookup"><span data-stu-id="5988b-279">So it cleans up Gen 0, Gen 1 and Gen 2 including LOH.</span></span> <span data-ttu-id="5988b-280">这称为完整垃圾回收，是最耗费时间的垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="5988b-280">This is called full garbage collection and is the most time-consuming garbage collection.</span></span> <span data-ttu-id="5988b-281">许多应用程序都可以接受。</span><span class="sxs-lookup"><span data-stu-id="5988b-281">For many applications, it can be acceptable.</span></span> <span data-ttu-id="5988b-282">但一定不能用于高性能的 web 服务器，在这种情况下，需要少量的大内存缓冲区来处理平均 web 请求 (从套接字读取、解压缩、解码 JSON & 更) 。</span><span class="sxs-lookup"><span data-stu-id="5988b-282">But definitely not for high-performance web servers, where few big memory buffers are needed to handle an average web request (read from a socket, decompress, decode JSON & more).</span></span>

<span data-ttu-id="5988b-283">将大型请求或响应正文存储到单个或中的 Naively `byte[]` `string` ：</span><span class="sxs-lookup"><span data-stu-id="5988b-283">Naively storing a large request or response body into a single `byte[]` or `string`:</span></span>

* <span data-ttu-id="5988b-284">可能会导致 LOH 中的空间快速耗尽。</span><span class="sxs-lookup"><span data-stu-id="5988b-284">May result in quickly running out of space in the LOH.</span></span>
* <span data-ttu-id="5988b-285">可能导致应用程序出现性能问题，因为正在运行完全 Gc。</span><span class="sxs-lookup"><span data-stu-id="5988b-285">May cause performance issues for the app because of full GCs running.</span></span>

## <a name="working-with-a-synchronous-data-processing-api"></a><span data-ttu-id="5988b-286">使用同步数据处理 API</span><span class="sxs-lookup"><span data-stu-id="5988b-286">Working with a synchronous data processing API</span></span>

<span data-ttu-id="5988b-287">使用仅支持同步读和写的序列化程序/反序列化程序时 (例如，  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)) ：</span><span class="sxs-lookup"><span data-stu-id="5988b-287">When using a serializer/de-serializer that only supports synchronous reads and writes (for example,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span></span>

* <span data-ttu-id="5988b-288">将数据异步缓冲到内存中，然后将其传递给序列化程序/反序列化程序。</span><span class="sxs-lookup"><span data-stu-id="5988b-288">Buffer the data into memory asynchronously before passing it into the serializer/de-serializer.</span></span>

> [!WARNING]
> <span data-ttu-id="5988b-289">如果请求很大，则可能会导致内存不足 (OOM) 情况。</span><span class="sxs-lookup"><span data-stu-id="5988b-289">If the request is large, it could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="5988b-290">OOM 可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="5988b-290">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="5988b-291">有关详细信息，请参阅本文档中的 [避免将大型请求正文或响应正文读入内存](#arlb) 中。</span><span class="sxs-lookup"><span data-stu-id="5988b-291">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="5988b-292"><xref:System.Text.Json>默认情况下，ASP.NET Core 3.0 使用 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="5988b-292">ASP.NET Core 3.0 uses <xref:System.Text.Json> by default for JSON serialization.</span></span> <span data-ttu-id="5988b-293"><xref:System.Text.Json>:</span><span class="sxs-lookup"><span data-stu-id="5988b-293"><xref:System.Text.Json>:</span></span>

* <span data-ttu-id="5988b-294">以异步方式读取和写入 JSON。</span><span class="sxs-lookup"><span data-stu-id="5988b-294">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="5988b-295">针对 UTF-8 文本进行了优化。</span><span class="sxs-lookup"><span data-stu-id="5988b-295">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="5988b-296">通常比 `Newtonsoft.Json` 性能更高。</span><span class="sxs-lookup"><span data-stu-id="5988b-296">Typically higher performance than `Newtonsoft.Json`.</span></span>

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a><span data-ttu-id="5988b-297">不要在字段中存储 IHttpContextAccessor</span><span class="sxs-lookup"><span data-stu-id="5988b-297">Do not store IHttpContextAccessor.HttpContext in a field</span></span>

<span data-ttu-id="5988b-298">[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) `HttpContext` 从请求线程访问时，IHttpContextAccessor 将返回活动请求的。</span><span class="sxs-lookup"><span data-stu-id="5988b-298">The [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) returns the `HttpContext` of the active request when accessed from the request thread.</span></span> <span data-ttu-id="5988b-299">`IHttpContextAccessor.HttpContext`**不**应存储在字段或变量中。</span><span class="sxs-lookup"><span data-stu-id="5988b-299">The `IHttpContextAccessor.HttpContext` should **not** be stored in a field or variable.</span></span>

<span data-ttu-id="5988b-300">请勿**执行此操作：** 下面的示例将存储 `HttpContext` 在字段中，并稍后尝试使用它。</span><span class="sxs-lookup"><span data-stu-id="5988b-300">**Do not do this:** The following example stores the `HttpContext` in a field and then attempts to use it later.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

<span data-ttu-id="5988b-301">前面的代码 `HttpContext` 在构造函数中经常捕获 null 或错误。</span><span class="sxs-lookup"><span data-stu-id="5988b-301">The preceding code frequently captures a null or incorrect `HttpContext` in the constructor.</span></span>

<span data-ttu-id="5988b-302">**执行以下操作：** 下面的示例：</span><span class="sxs-lookup"><span data-stu-id="5988b-302">**Do this:** The following example:</span></span>

* <span data-ttu-id="5988b-303">将存储 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 在字段中。</span><span class="sxs-lookup"><span data-stu-id="5988b-303">Stores the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in a field.</span></span>
* <span data-ttu-id="5988b-304">`HttpContext`在正确的时间使用字段并检查 `null` 。</span><span class="sxs-lookup"><span data-stu-id="5988b-304">Uses the `HttpContext` field at the correct time and checks for `null`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a><span data-ttu-id="5988b-305">不要从多个线程访问 HttpContext</span><span class="sxs-lookup"><span data-stu-id="5988b-305">Do not access HttpContext from multiple threads</span></span>

<span data-ttu-id="5988b-306">`HttpContext`*不*是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="5988b-306">`HttpContext` is *NOT* thread-safe.</span></span> <span data-ttu-id="5988b-307">`HttpContext`并行从多个线程进行访问可能会导致未定义的行为，如挂起、崩溃和数据损坏。</span><span class="sxs-lookup"><span data-stu-id="5988b-307">Accessing `HttpContext` from multiple threads in parallel can result in undefined behavior such as hangs, crashes, and data corruption.</span></span>

<span data-ttu-id="5988b-308">请勿**执行此操作：** 下面的示例执行三个并行请求，并在传出 HTTP 请求之前和之后记录传入的请求路径。</span><span class="sxs-lookup"><span data-stu-id="5988b-308">**Do not do this:** The following example makes three parallel requests and logs the incoming request path before and after the outgoing HTTP request.</span></span> <span data-ttu-id="5988b-309">可以从多个线程访问请求路径，可能会并行进行。</span><span class="sxs-lookup"><span data-stu-id="5988b-309">The request path is accessed from multiple threads, potentially in parallel.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

<span data-ttu-id="5988b-310">**执行以下操作：** 下面的示例在发出三个并行请求之前复制传入请求中的所有数据。</span><span class="sxs-lookup"><span data-stu-id="5988b-310">**Do this:** The following example copies all data from the incoming request before making the three parallel requests.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a><span data-ttu-id="5988b-311">请求完成后，不要使用 HttpContext</span><span class="sxs-lookup"><span data-stu-id="5988b-311">Do not use the HttpContext after the request is complete</span></span>

<span data-ttu-id="5988b-312">`HttpContext` 只有在 ASP.NET Core 管道中存在活动 HTTP 请求时，它才有效。</span><span class="sxs-lookup"><span data-stu-id="5988b-312">`HttpContext` is only valid as long as there is an active HTTP request in the ASP.NET Core pipeline.</span></span> <span data-ttu-id="5988b-313">整个 ASP.NET Core 管道是一系列执行每个请求的委托。</span><span class="sxs-lookup"><span data-stu-id="5988b-313">The entire ASP.NET Core pipeline is an asynchronous chain of delegates that executes every request.</span></span> <span data-ttu-id="5988b-314">当从此 `Task` 链返回的完成时， `HttpContext` 会回收。</span><span class="sxs-lookup"><span data-stu-id="5988b-314">When the `Task` returned from this chain completes, the `HttpContext` is recycled.</span></span>

<span data-ttu-id="5988b-315">请勿**执行此操作：** 下面的示例使用 `async void` ，当达到第一个时，它将使 HTTP 请求完成 `await` ：</span><span class="sxs-lookup"><span data-stu-id="5988b-315">**Do not do this:** The following example uses `async void` which makes the HTTP request complete when the first `await` is reached:</span></span>

* <span data-ttu-id="5988b-316">在 ASP.NET Core 应用程序中，这 **始终** 是一种不好的做法。</span><span class="sxs-lookup"><span data-stu-id="5988b-316">Which is **ALWAYS** a bad practice in ASP.NET Core apps.</span></span>
* <span data-ttu-id="5988b-317">`HttpResponse`HTTP 请求完成后访问。</span><span class="sxs-lookup"><span data-stu-id="5988b-317">Accesses the `HttpResponse` after the HTTP request is complete.</span></span>
* <span data-ttu-id="5988b-318">崩溃进程。</span><span class="sxs-lookup"><span data-stu-id="5988b-318">Crashes the process.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

<span data-ttu-id="5988b-319">**执行以下操作：** 下面的示例将返回 `Task` 到框架，以便在操作完成之前，不会完成 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="5988b-319">**Do this:** The following example returns a `Task` to the framework, so the HTTP request doesn't complete until the action completes.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a><span data-ttu-id="5988b-320">不要捕获后台线程中的 HttpContext</span><span class="sxs-lookup"><span data-stu-id="5988b-320">Do not capture the HttpContext in background threads</span></span>

<span data-ttu-id="5988b-321">请勿**执行此操作：** 下面的示例演示关闭 `HttpContext` 从 `Controller` 属性捕获。</span><span class="sxs-lookup"><span data-stu-id="5988b-321">**Do not do this:** The following example shows a closure is capturing the `HttpContext` from the `Controller` property.</span></span> <span data-ttu-id="5988b-322">这是一种不好的做法，因为工作项可以：</span><span class="sxs-lookup"><span data-stu-id="5988b-322">This is a bad practice because the work item could:</span></span>

* <span data-ttu-id="5988b-323">在请求范围之外运行。</span><span class="sxs-lookup"><span data-stu-id="5988b-323">Run outside of the request scope.</span></span>
* <span data-ttu-id="5988b-324">尝试读取错误 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="5988b-324">Attempt to read the wrong `HttpContext`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

<span data-ttu-id="5988b-325">**执行以下操作：** 下面的示例：</span><span class="sxs-lookup"><span data-stu-id="5988b-325">**Do this:** The following example:</span></span>

* <span data-ttu-id="5988b-326">在请求过程中复制后台任务所需的数据。</span><span class="sxs-lookup"><span data-stu-id="5988b-326">Copies the data required in the background task during the request.</span></span>
* <span data-ttu-id="5988b-327">不从控制器引用任何内容。</span><span class="sxs-lookup"><span data-stu-id="5988b-327">Doesn't reference anything from the controller.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

<span data-ttu-id="5988b-328">应将后台任务作为托管服务实现。</span><span class="sxs-lookup"><span data-stu-id="5988b-328">Background tasks should be implemented as hosted services.</span></span> <span data-ttu-id="5988b-329">有关详细信息，请参阅[使用托管服务的后台任务](xref:fundamentals/host/hosted-services)。</span><span class="sxs-lookup"><span data-stu-id="5988b-329">For more information, see [Background tasks with hosted services](xref:fundamentals/host/hosted-services).</span></span>

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a><span data-ttu-id="5988b-330">不要捕获注入到后台线程控制器的服务</span><span class="sxs-lookup"><span data-stu-id="5988b-330">Do not capture services injected into the controllers on background threads</span></span>

<span data-ttu-id="5988b-331">请勿**执行此操作：** 下面的示例演示关闭 `DbContext` `Controller` 操作从操作参数捕获。</span><span class="sxs-lookup"><span data-stu-id="5988b-331">**Do not do this:** The following example shows a closure is capturing the `DbContext` from the `Controller` action parameter.</span></span> <span data-ttu-id="5988b-332">这是一种不好的做法。</span><span class="sxs-lookup"><span data-stu-id="5988b-332">This is a bad practice.</span></span>  <span data-ttu-id="5988b-333">工作项可以在请求范围之外运行。</span><span class="sxs-lookup"><span data-stu-id="5988b-333">The work item could run outside of the request scope.</span></span> <span data-ttu-id="5988b-334">的 `ContosoDbContext` 作用域限定为请求，导致 `ObjectDisposedException` 。</span><span class="sxs-lookup"><span data-stu-id="5988b-334">The `ContosoDbContext` is scoped to the request, resulting in an `ObjectDisposedException`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

<span data-ttu-id="5988b-335">**执行以下操作：** 下面的示例：</span><span class="sxs-lookup"><span data-stu-id="5988b-335">**Do this:** The following example:</span></span>

* <span data-ttu-id="5988b-336">注入，以便在 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 后台工作项中创建范围。</span><span class="sxs-lookup"><span data-stu-id="5988b-336">Injects an <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> in order to create a scope in the background work item.</span></span> <span data-ttu-id="5988b-337">`IServiceScopeFactory` 是单一实例。</span><span class="sxs-lookup"><span data-stu-id="5988b-337">`IServiceScopeFactory` is a singleton.</span></span>
* <span data-ttu-id="5988b-338">在后台线程中创建新的依赖项注入范围。</span><span class="sxs-lookup"><span data-stu-id="5988b-338">Creates a new dependency injection scope in the background thread.</span></span>
* <span data-ttu-id="5988b-339">不从控制器引用任何内容。</span><span class="sxs-lookup"><span data-stu-id="5988b-339">Doesn't reference anything from the controller.</span></span>
* <span data-ttu-id="5988b-340">不 `ContosoDbContext` 从传入请求中捕获。</span><span class="sxs-lookup"><span data-stu-id="5988b-340">Doesn't capture the `ContosoDbContext` from the incoming request.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

<span data-ttu-id="5988b-341">以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="5988b-341">The following highlighted code:</span></span>

* <span data-ttu-id="5988b-342">在后台操作的生存期内创建一个范围，并从中解析服务。</span><span class="sxs-lookup"><span data-stu-id="5988b-342">Creates a scope for the lifetime of the background operation and resolves services from it.</span></span>
* <span data-ttu-id="5988b-343">使用 `ContosoDbContext` 正确的作用域。</span><span class="sxs-lookup"><span data-stu-id="5988b-343">Uses `ContosoDbContext` from the correct scope.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a><span data-ttu-id="5988b-344">请不要在响应正文开始后修改状态代码或标头</span><span class="sxs-lookup"><span data-stu-id="5988b-344">Do not modify the status code or headers after the response body has started</span></span>

<span data-ttu-id="5988b-345">ASP.NET Core 不会缓冲 HTTP 响应正文。</span><span class="sxs-lookup"><span data-stu-id="5988b-345">ASP.NET Core does not buffer the HTTP response body.</span></span> <span data-ttu-id="5988b-346">第一次写入响应时：</span><span class="sxs-lookup"><span data-stu-id="5988b-346">The first time the response is written:</span></span>

* <span data-ttu-id="5988b-347">标头将与主体块区一起发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="5988b-347">The headers are sent along with that chunk of the body to the client.</span></span>
* <span data-ttu-id="5988b-348">不能再更改响应标头。</span><span class="sxs-lookup"><span data-stu-id="5988b-348">It's no longer possible to change response headers.</span></span>

<span data-ttu-id="5988b-349">请勿**执行此操作：** 以下代码在响应已启动之后尝试添加响应标头：</span><span class="sxs-lookup"><span data-stu-id="5988b-349">**Do not do this:** The following code tries to add response headers after the response has already started:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

<span data-ttu-id="5988b-350">在前面的代码中， `context.Response.Headers["test"] = "test value";` 如果 `next()` 已写入响应，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="5988b-350">In the preceding code, `context.Response.Headers["test"] = "test value";` will throw an exception if `next()` has written to the response.</span></span>

<span data-ttu-id="5988b-351">**执行以下操作：** 下面的示例在修改标头之前检查 HTTP 响应是否已启动。</span><span class="sxs-lookup"><span data-stu-id="5988b-351">**Do this:** The following example checks if the HTTP response has started before modifying the headers.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

<span data-ttu-id="5988b-352">**执行以下操作：** 下面的示例使用在 `HttpResponse.OnStarting` 将响应标头刷新到客户端之前设置标头。</span><span class="sxs-lookup"><span data-stu-id="5988b-352">**Do this:** The following example uses `HttpResponse.OnStarting` to set the headers before the response headers are flushed to the client.</span></span>

<span data-ttu-id="5988b-353">如果检查响应是否尚未启动，则允许注册将在写入响应标头之前调用的回调。</span><span class="sxs-lookup"><span data-stu-id="5988b-353">Checking if the response has not started allows registering a callback that will be invoked just before response headers are written.</span></span> <span data-ttu-id="5988b-354">检查响应是否尚未开始：</span><span class="sxs-lookup"><span data-stu-id="5988b-354">Checking if the response has not started:</span></span>

* <span data-ttu-id="5988b-355">提供了随时追加或重写标头的功能。</span><span class="sxs-lookup"><span data-stu-id="5988b-355">Provides the ability to append or override headers just in time.</span></span>
* <span data-ttu-id="5988b-356">不需要了解管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="5988b-356">Doesn't require knowledge of the next middleware in the pipeline.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a><span data-ttu-id="5988b-357">如果已开始写入响应正文，请不要调用下一个 ( # A1</span><span class="sxs-lookup"><span data-stu-id="5988b-357">Do not call next() if you have already started writing to the response body</span></span>

<span data-ttu-id="5988b-358">仅当组件可以处理和操作响应时，才应调用组件。</span><span class="sxs-lookup"><span data-stu-id="5988b-358">Components only expect to be called if it's possible for them to handle and manipulate the response.</span></span>

## <a name="use-in-process-hosting-with-iis"></a><span data-ttu-id="5988b-359">使用 IIS 中的进程内托管</span><span class="sxs-lookup"><span data-stu-id="5988b-359">Use In-process hosting with IIS</span></span>

<span data-ttu-id="5988b-360">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="5988b-360">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="5988b-361">进程内托管提供了对进程外托管的性能改进，因为请求未通过环回适配器进行代理。</span><span class="sxs-lookup"><span data-stu-id="5988b-361">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="5988b-362">环回适配器是一种将传出的网络流量返回到相同计算机的网络接口。</span><span class="sxs-lookup"><span data-stu-id="5988b-362">The loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="5988b-363">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="5988b-363">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5988b-364">项目默认为 ASP.NET Core 3.0 及更高版本中的进程内承载模型。</span><span class="sxs-lookup"><span data-stu-id="5988b-364">Projects default to the in-process hosting model in ASP.NET Core 3.0 and later.</span></span>

<span data-ttu-id="5988b-365">有关详细信息，请参阅 [在 Windows 上利用 IIS 进行主机 ASP.NET Core](xref:host-and-deploy/iis/index)</span><span class="sxs-lookup"><span data-stu-id="5988b-365">For more information, see [Host ASP.NET Core on Windows with IIS](xref:host-and-deploy/iis/index)</span></span>
