---
title: ASP.NET Core 性能最佳做法
author: mjrousos
description: 在 ASP.NET Core 应用中提高性能并避免出现常见性能问题的提示。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 12/05/2019
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: bd30776d527b4ac9f44005e9f5d03fec7cfda2e6
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880923"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="c7b1d-103">ASP.NET Core 性能最佳做法</span><span class="sxs-lookup"><span data-stu-id="c7b1d-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="c7b1d-104">作者： [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="c7b1d-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="c7b1d-105">本文提供了有关 ASP.NET Core 的性能最佳做法的准则。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-105">This article provides guidelines for performance best practices with ASP.NET Core.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="c7b1d-106">主动缓存</span><span class="sxs-lookup"><span data-stu-id="c7b1d-106">Cache aggressively</span></span>

<span data-ttu-id="c7b1d-107">此文档的几个部分讨论了缓存。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-107">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="c7b1d-108">有关更多信息，请参见<xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-108">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="understand-hot-code-paths"></a><span data-ttu-id="c7b1d-109">了解热代码路径</span><span class="sxs-lookup"><span data-stu-id="c7b1d-109">Understand hot code paths</span></span>

<span data-ttu-id="c7b1d-110">在本文档中，将*热代码路径*定义为经常调用的代码路径和执行时间量。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-110">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="c7b1d-111">热代码路径通常限制应用向外缩放和性能，并将在本文档的几个部分中进行讨论。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-111">Hot code paths typically limit app scale-out and performance and are discussed in several parts of this document.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="c7b1d-112">避免阻止调用</span><span class="sxs-lookup"><span data-stu-id="c7b1d-112">Avoid blocking calls</span></span>

<span data-ttu-id="c7b1d-113">应将 ASP.NET Core 应用程序设计为同时处理许多请求。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-113">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="c7b1d-114">异步 Api 允许一小部分线程通过不等待阻止调用来处理上千个并发请求。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-114">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="c7b1d-115">线程可以处理另一请求，而不是等待长时间运行的同步任务完成。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-115">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="c7b1d-116">ASP.NET Core 应用中的常见性能问题是阻止可能是异步的调用。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-116">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="c7b1d-117">很多同步阻塞调用会导致[线程池](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)不足并降低响应时间。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-117">Many synchronous blocking calls lead to [Thread Pool starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) and degraded response times.</span></span>

<span data-ttu-id="c7b1d-118">**请勿**：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-118">**Do not**:</span></span>

* <span data-ttu-id="c7b1d-119">通过调用[task. Wait](/dotnet/api/system.threading.tasks.task.wait)或[task.](/dotnet/api/system.threading.tasks.task-1.result)来阻止异步执行。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-119">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="c7b1d-120">获取通用代码路径中的锁。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-120">Acquire locks in common code paths.</span></span> <span data-ttu-id="c7b1d-121">当构建为并行运行代码时，ASP.NET Core 应用程序的性能最高。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-121">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>
* <span data-ttu-id="c7b1d-122">调用[任务。运行](/dotnet/api/system.threading.tasks.task.run)并立即等待。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-122">Call [Task.Run](/dotnet/api/system.threading.tasks.task.run) and immediately await it.</span></span> <span data-ttu-id="c7b1d-123">ASP.NET Core 已在正常线程池线程上运行应用程序代码，因此调用任务。运行仅会导致额外的不必要的线程池计划。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-123">ASP.NET Core already runs app code on normal Thread Pool threads, so calling Task.Run only results in extra unnecessary Thread Pool scheduling.</span></span> <span data-ttu-id="c7b1d-124">即使计划的代码会阻止线程，任务也不会阻止。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-124">Even if the scheduled code would block a thread, Task.Run does not prevent that.</span></span>

<span data-ttu-id="c7b1d-125">**建议做法**：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-125">**Do**:</span></span>

* <span data-ttu-id="c7b1d-126">使[热代码路径](#understand-hot-code-paths)处于异步状态。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-126">Make [hot code paths](#understand-hot-code-paths) asynchronous.</span></span>
* <span data-ttu-id="c7b1d-127">如果异步 API 可用，则异步调用数据访问和长时间运行的操作 Api。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-127">Call data access and long-running operations APIs asynchronously if an asynchronous API is available.</span></span> <span data-ttu-id="c7b1d-128">同样，不要使用[任务。运行](/dotnet/api/system.threading.tasks.task.run)以使 synchronus API 成为异步。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-128">Once again, do not use [Task.Run](/dotnet/api/system.threading.tasks.task.run) to make a synchronus API asynchronous.</span></span>
* <span data-ttu-id="c7b1d-129">使控制器/Razor 页面操作异步。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-129">Make controller/Razor Page actions asynchronous.</span></span> <span data-ttu-id="c7b1d-130">为了受益于[async/await](/dotnet/csharp/programming-guide/concepts/async/)模式，整个调用堆栈是异步的。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-130">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="c7b1d-131">探查器（如[PerfView](https://github.com/Microsoft/perfview)）可用于查找频繁添加到[线程池中](/windows/desktop/procthread/thread-pools)的线程。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-131">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="c7b1d-132">`Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` 事件表示添加到线程池中的线程。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-132">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="c7b1d-133">最小化大型对象分配</span><span class="sxs-lookup"><span data-stu-id="c7b1d-133">Minimize large object allocations</span></span>

<span data-ttu-id="c7b1d-134">[.Net Core 垃圾回收器](/dotnet/standard/garbage-collection/)在 ASP.NET Core 应用中自动管理内存的分配和释放。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-134">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="c7b1d-135">自动垃圾回收通常意味着开发人员无需担心如何或何时释放内存。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-135">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="c7b1d-136">但是，清理未引用的对象会占用 CPU 时间，因此开发人员应最大限度地减少[热代码路径](#understand-hot-code-paths)中的对象分配。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-136">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#understand-hot-code-paths).</span></span> <span data-ttu-id="c7b1d-137">垃圾回收对于大型对象（> 85 K 字节）特别昂贵。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-137">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="c7b1d-138">大型对象存储在[大型对象堆](/dotnet/standard/garbage-collection/large-object-heap)上，并要求进行完整（第2代）垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-138">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="c7b1d-139">与第0代和第1代回收不同，第2代回收需要临时暂停应用执行。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-139">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="c7b1d-140">频繁分配和取消分配大型对象会导致性能不一致。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-140">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="c7b1d-141">建议：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-141">Recommendations:</span></span>

* <span data-ttu-id="c7b1d-142">**请考虑缓存**经常使用的大型对象。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-142">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="c7b1d-143">缓存大型对象会阻止开销较高的分配。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-143">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="c7b1d-144">使用[ArrayPool\<t >](/dotnet/api/system.buffers.arraypool-1)来存储大型数组，从而对缓冲区**进行**缓冲。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-144">**Do** pool buffers by using an [ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="c7b1d-145">**不要**在[热代码路径](#understand-hot-code-paths)上分配很多生存期较短的大型对象。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-145">**Do not** allocate many, short-lived large objects on [hot code paths](#understand-hot-code-paths).</span></span>

<span data-ttu-id="c7b1d-146">可以通过查看[PerfView](https://github.com/Microsoft/perfview)中的垃圾回收（GC）统计信息并进行检查来诊断内存问题，例如前面的问题：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-146">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="c7b1d-147">垃圾回收暂停时间。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-147">Garbage collection pause time.</span></span>
* <span data-ttu-id="c7b1d-148">垃圾回收所用的处理器时间百分比。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-148">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="c7b1d-149">第0代、第1代和第2代垃圾回收量。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-149">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="c7b1d-150">有关详细信息，请参阅[垃圾回收和性能](/dotnet/standard/garbage-collection/performance)。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-150">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access"></a><span data-ttu-id="c7b1d-151">优化数据访问</span><span class="sxs-lookup"><span data-stu-id="c7b1d-151">Optimize Data Access</span></span>

<span data-ttu-id="c7b1d-152">与数据存储和其他远程服务的交互通常是 ASP.NET Core 应用程序的最慢部分。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-152">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="c7b1d-153">有效读取和写入数据对于良好的性能至关重要。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-153">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="c7b1d-154">建议：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-154">Recommendations:</span></span>

* <span data-ttu-id="c7b1d-155">**请**以异步方式调用所有数据访问 api。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-155">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="c7b1d-156">检索的数据**不**是必需的。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-156">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="c7b1d-157">编写查询以仅返回当前 HTTP 请求所必需的数据。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-157">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="c7b1d-158">如果数据可以接受，**请考虑缓存**经常访问的从数据库或远程服务检索的数据。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-158">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="c7b1d-159">使用[MemoryCache](xref:performance/caching/memory)或[microsoft.web.distributedcache](xref:performance/caching/distributed)，具体取决于方案。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-159">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="c7b1d-160">有关更多信息，请参见<xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-160">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="c7b1d-161">**尽量减少**网络往返次数。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-161">**Do** minimize network round trips.</span></span> <span data-ttu-id="c7b1d-162">目标是使用单个调用而不是多个调用来检索所需数据。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-162">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="c7b1d-163">在 Entity Framework Core 中，当出于只读目的访问数据时，**使用** [no-tracking 查询](/ef/core/querying/tracking#no-tracking-queries) 。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-163">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="c7b1d-164">EF Core 可以更有效地返回非跟踪查询的结果。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-164">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="c7b1d-165">**筛选和**聚合 LINQ 查询（例如，使用 `.Where`、`.Select`或 `.Sum` 语句），以便数据库执行筛选。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-165">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="c7b1d-166">**请考虑 EF Core**在客户端上解析一些查询运算符，这可能导致查询执行效率低下。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-166">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="c7b1d-167">有关详细信息，请参阅[客户端评估性能问题](/ef/core/querying/client-eval#client-evaluation-performance-issues)。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-167">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="c7b1d-168">**不要**对集合使用投影查询，这可能会导致执行 "N + 1" 个 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-168">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="c7b1d-169">有关详细信息，请参阅[相关子查询的优化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-169">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="c7b1d-170">请参阅[EF 高性能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)，了解可提高大规模应用程序性能的方法：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-170">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="c7b1d-171">DbContext 池</span><span class="sxs-lookup"><span data-stu-id="c7b1d-171">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="c7b1d-172">显式编译的查询</span><span class="sxs-lookup"><span data-stu-id="c7b1d-172">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="c7b1d-173">建议在提交基本代码之前测量前面的高性能方法的影响。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-173">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="c7b1d-174">已编译查询的额外复杂性可能不会提高性能。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-174">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="c7b1d-175">通过查看[Application Insights](/azure/application-insights/app-insights-overview)或分析工具访问数据所用的时间，可以检测到查询问题。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-175">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="c7b1d-176">大多数数据库还提供有关频繁执行的查询的统计信息。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-176">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="c7b1d-177">与 HttpClientFactory 建立池 HTTP 连接</span><span class="sxs-lookup"><span data-stu-id="c7b1d-177">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="c7b1d-178">尽管[HttpClient](/dotnet/api/system.net.http.httpclient)实现 `IDisposable` 接口，但它是为重复使用而设计的。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-178">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="c7b1d-179">关闭 `HttpClient` 实例会使套接字在一小段时间内打开 `TIME_WAIT` 状态。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-179">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="c7b1d-180">如果经常使用创建和释放 `HttpClient` 对象的代码路径，应用程序可能会耗尽可用的套接字。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-180">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="c7b1d-181">ASP.NET Core 2.1 中引入了[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)作为此问题的解决方案。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-181">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="c7b1d-182">它处理池 HTTP 连接以优化性能和可靠性。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-182">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="c7b1d-183">建议：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-183">Recommendations:</span></span>

* <span data-ttu-id="c7b1d-184">**不要**直接创建和释放 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-184">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="c7b1d-185">**请**使用[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)检索 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-185">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="c7b1d-186">有关详细信息，请参阅[使用 HttpClientFactory 实现复原 HTTP 请求](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-186">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="c7b1d-187">快速保持通用代码路径</span><span class="sxs-lookup"><span data-stu-id="c7b1d-187">Keep common code paths fast</span></span>

<span data-ttu-id="c7b1d-188">您希望所有的代码都是快速的，通常称为代码路径是最重要的，可进行优化：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-188">You want all of your code to be fast, frequently called code paths are the most critical to optimize:</span></span>

* <span data-ttu-id="c7b1d-189">应用程序的请求处理管道中的中间件组件，尤其是在管道早期运行的中间件。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-189">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="c7b1d-190">这些组件会对性能产生很大的影响。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-190">These components have a large impact on performance.</span></span>
* <span data-ttu-id="c7b1d-191">针对每个请求或每个请求多次执行的代码。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-191">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="c7b1d-192">例如，自定义日志记录、授权处理程序或暂时性服务的初始化。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-192">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="c7b1d-193">建议：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-193">Recommendations:</span></span>

* <span data-ttu-id="c7b1d-194">**不要**将自定义中间件组件用于长时间运行的任务。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-194">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="c7b1d-195">**请使用性能**分析工具（如[Visual Studio 诊断工具](/visualstudio/profiling/profiling-feature-tour)或[PerfView](https://github.com/Microsoft/perfview)）来识别[热代码路径](#understand-hot-code-paths)。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-195">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#understand-hot-code-paths).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="c7b1d-196">在 HTTP 请求之外完成长时间运行的任务</span><span class="sxs-lookup"><span data-stu-id="c7b1d-196">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="c7b1d-197">大多数对 ASP.NET Core 应用程序的请求都可以通过控制器或页面模型进行处理，该模型调用必要的服务并返回 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-197">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="c7b1d-198">对于涉及长时间运行的任务的某些请求，最好将整个请求响应过程设为异步处理。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-198">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="c7b1d-199">建议：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-199">Recommendations:</span></span>

* <span data-ttu-id="c7b1d-200">**请**不要等待长时间运行的任务在普通的 HTTP 请求处理过程中完成。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-200">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="c7b1d-201">**请考虑使用**[后台服务](xref:fundamentals/host/hosted-services)处理长时间运行的请求，或使用[Azure 函数](/azure/azure-functions/)处理进程外的请求。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-201">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="c7b1d-202">在进程外完成工作对于 CPU 密集型任务特别有用。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-202">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="c7b1d-203">**请使用实时**通信选项（如[SignalR](xref:signalr/introduction)）以异步方式与客户端进行通信。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-203">**Do** use real-time communication options, such as [SignalR](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="c7b1d-204">缩小客户端资产</span><span class="sxs-lookup"><span data-stu-id="c7b1d-204">Minify client assets</span></span>

<span data-ttu-id="c7b1d-205">具有复杂前端的 ASP.NET Core 应用通常会提供许多 JavaScript、CSS 或图像文件。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-205">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="c7b1d-206">可以通过以下方式改善初始负载请求的性能：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-206">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="c7b1d-207">绑定，将多个文件合并到一个文件中。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-207">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="c7b1d-208">缩小，它通过删除空白和注释来减小文件大小。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-208">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="c7b1d-209">建议：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-209">Recommendations:</span></span>

* <span data-ttu-id="c7b1d-210">**请**使用 ASP.NET Core 的[内置支持](xref:client-side/bundling-and-minification)，以便对客户端资产进行捆绑和缩小。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-210">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="c7b1d-211">**请考虑其他**第三方工具（如[Webpack](https://webpack.js.org/)），以实现复杂的客户端资产管理。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-211">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="c7b1d-212">压缩响应</span><span class="sxs-lookup"><span data-stu-id="c7b1d-212">Compress responses</span></span>

 <span data-ttu-id="c7b1d-213">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-213">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="c7b1d-214">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-214">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="c7b1d-215">有关详细信息，请参阅[响应压缩](xref:performance/response-compression)。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-215">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="c7b1d-216">使用最新 ASP.NET Core 版本</span><span class="sxs-lookup"><span data-stu-id="c7b1d-216">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="c7b1d-217">ASP.NET Core 的每个新版本都包括性能改进。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-217">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="c7b1d-218">.NET Core 和 ASP.NET Core 中的优化意味着较新版本通常优于较旧的版本。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-218">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="c7b1d-219">例如，.NET Core 2.1 添加了对[跨\<t >](https://msdn.microsoft.com/magazine/mt814808.aspx)中已编译的正则表达式和获益的支持。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-219">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx).</span></span> <span data-ttu-id="c7b1d-220">ASP.NET Core 2.2 添加了对 HTTP/2 的支持。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-220">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="c7b1d-221">[ASP.NET Core 3.0 添加了许多改进](xref:aspnetcore-3.0)，减少了内存使用量并提高了吞吐量。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-221">[ASP.NET Core 3.0 adds many improvements](xref:aspnetcore-3.0) that reduce memory usage and improve throughput.</span></span> <span data-ttu-id="c7b1d-222">如果性能是优先考虑的，请考虑升级到 ASP.NET Core 的当前版本。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-222">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

## <a name="minimize-exceptions"></a><span data-ttu-id="c7b1d-223">最小化异常</span><span class="sxs-lookup"><span data-stu-id="c7b1d-223">Minimize exceptions</span></span>

<span data-ttu-id="c7b1d-224">异常应极少。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-224">Exceptions should be rare.</span></span> <span data-ttu-id="c7b1d-225">相对于其他代码流模式，引发和捕获异常的速度很慢。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-225">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="c7b1d-226">因此，不应使用异常来控制正常的程序流。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-226">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="c7b1d-227">建议：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-227">Recommendations:</span></span>

* <span data-ttu-id="c7b1d-228">**不要**使用引发或捕获异常作为正常程序流的方法，尤其是在[热代码路径](#understand-hot-code-paths)中。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-228">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#understand-hot-code-paths).</span></span>
* <span data-ttu-id="c7b1d-229">在应用程序**中包括逻辑**，以检测和处理会导致异常的情况。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-229">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="c7b1d-230">**引发或**捕获异常或意外情况的异常。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-230">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="c7b1d-231">应用诊断工具（如 Application Insights）可帮助识别应用中可能影响性能的常见异常。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-231">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>

## <a name="performance-and-reliability"></a><span data-ttu-id="c7b1d-232">性能和可靠性</span><span class="sxs-lookup"><span data-stu-id="c7b1d-232">Performance and reliability</span></span>

<span data-ttu-id="c7b1d-233">以下各节提供了性能提示以及已知的可靠性问题和解决方案。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-233">The following sections provide performance tips and known reliability problems and solutions.</span></span>

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a><span data-ttu-id="c7b1d-234">避免 HttpRequest/Httpresponse.cache 正文上的同步读取或写入</span><span class="sxs-lookup"><span data-stu-id="c7b1d-234">Avoid synchronous read or write on HttpRequest/HttpResponse body</span></span>

<span data-ttu-id="c7b1d-235">ASP.NET Core 中的所有 IO 都是异步的。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-235">All IO in ASP.NET Core is asynchronous.</span></span> <span data-ttu-id="c7b1d-236">服务器实现 `Stream` 接口，该接口具有同步和异步重载。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-236">Servers implement the `Stream` interface, which has both synchronous and asynchronous overloads.</span></span> <span data-ttu-id="c7b1d-237">应首选异步文件以避免阻塞线程池线程。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-237">The asynchronous ones should be preferred to avoid blocking thread pool threads.</span></span> <span data-ttu-id="c7b1d-238">阻塞线程可能会导致线程池不足。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-238">Blocking threads can lead to thread pool starvation.</span></span>

<span data-ttu-id="c7b1d-239">请勿**执行此操作：** 下面的示例使用 <xref:System.IO.StreamReader.ReadToEnd*>。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-239">**Do not do this:** The following example uses the <xref:System.IO.StreamReader.ReadToEnd*>.</span></span> <span data-ttu-id="c7b1d-240">此方法阻止当前线程等待结果。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-240">It blocks the current thread to wait for the result.</span></span> <span data-ttu-id="c7b1d-241">这是一个[通过异步同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)的示例。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-241">This is an example of [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

<span data-ttu-id="c7b1d-242">在前面的代码中，`Get` 以同步方式将整个 HTTP 请求正文读入内存中。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-242">In the preceding code, `Get` synchronously reads the entire HTTP request body into memory.</span></span> <span data-ttu-id="c7b1d-243">如果客户端缓慢上传，则应用通过异步执行同步。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-243">If the client is slowly uploading, the app is doing sync over async.</span></span> <span data-ttu-id="c7b1d-244">应用通过异步同步，因为 Kestrel**不支持同步**读取。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-244">The app does sync over async because Kestrel does **NOT** support synchronous reads.</span></span>

<span data-ttu-id="c7b1d-245">**执行以下操作：** 下面的示例使用 <xref:System.IO.StreamReader.ReadToEndAsync*>，在读取时不会阻止线程。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-245">**Do this:** The following example uses <xref:System.IO.StreamReader.ReadToEndAsync*> and does not block the thread while reading.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

<span data-ttu-id="c7b1d-246">前面的代码异步将整个 HTTP 请求正文读入内存中。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-246">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="c7b1d-247">如果请求很大，则将整个 HTTP 请求正文读取到内存中可能会导致内存不足（OOM）。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-247">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="c7b1d-248">OOM 可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-248">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="c7b1d-249">有关详细信息，请参阅本文档中的[避免将大型请求正文或响应正文读入内存](#arlb)中。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-249">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="c7b1d-250">**执行以下操作：** 下面的示例使用非缓冲请求正文完全异步：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-250">**Do this:** The following example is fully asynchronous using a non buffered request body:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

<span data-ttu-id="c7b1d-251">前面的代码将请求正文异步反序列化为C#对象。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-251">The preceding code asynchronously de-serializes the request body into a C# object.</span></span>

## <a name="prefer-readformasync-over-requestform"></a><span data-ttu-id="c7b1d-252">首选 ReadFormAsync over 请求。窗体</span><span class="sxs-lookup"><span data-stu-id="c7b1d-252">Prefer ReadFormAsync over Request.Form</span></span>

<span data-ttu-id="c7b1d-253">使用 `HttpContext.Request.ReadFormAsync` 而非 `HttpContext.Request.Form`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-253">Use `HttpContext.Request.ReadFormAsync` instead of `HttpContext.Request.Form`.</span></span>
<span data-ttu-id="c7b1d-254">仅在以下情况下，才能安全地读取 `HttpContext.Request.Form`：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-254">`HttpContext.Request.Form` can be safely read only with the following conditions:</span></span>

* <span data-ttu-id="c7b1d-255">已通过调用 `ReadFormAsync`读取了窗体，且</span><span class="sxs-lookup"><span data-stu-id="c7b1d-255">The form has been read by a call to `ReadFormAsync`, and</span></span>
* <span data-ttu-id="c7b1d-256">正在使用读取缓存的窗体值 `HttpContext.Request.Form`</span><span class="sxs-lookup"><span data-stu-id="c7b1d-256">The cached form value is being read using `HttpContext.Request.Form`</span></span>

<span data-ttu-id="c7b1d-257">请勿**执行此操作：** 下面的示例使用 `HttpContext.Request.Form`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-257">**Do not do this:** The following example uses `HttpContext.Request.Form`.</span></span>  <span data-ttu-id="c7b1d-258">`HttpContext.Request.Form`[通过异步使用同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)，可能会导致线程池不足。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-258">`HttpContext.Request.Form` uses [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) and can lead to thread pool starvation.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

<span data-ttu-id="c7b1d-259">**执行以下操作：** 下面的示例使用 `HttpContext.Request.ReadFormAsync` 以异步方式读取窗体体。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-259">**Do this:** The following example uses `HttpContext.Request.ReadFormAsync` to read the form body asynchronously.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a><span data-ttu-id="c7b1d-260">避免将大型请求正文或响应正文读入内存</span><span class="sxs-lookup"><span data-stu-id="c7b1d-260">Avoid reading large request bodies or response bodies into memory</span></span>

<span data-ttu-id="c7b1d-261">在 .NET 中，大于 85 KB 的每个对象分配将在大型对象堆（[LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)）中结束。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-261">In .NET, every object allocation greater than 85 KB ends up in the large object heap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)).</span></span> <span data-ttu-id="c7b1d-262">大型对象的开销很大：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-262">Large objects are expensive in two ways:</span></span>

* <span data-ttu-id="c7b1d-263">分配开销较高，因为必须清除新分配的大型对象的内存。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-263">The allocation cost is high because the memory for a newly allocated large object has to be cleared.</span></span> <span data-ttu-id="c7b1d-264">CLR 确保清除所有新分配对象的内存。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-264">The CLR guarantees that memory for all newly allocated objects is cleared.</span></span>
* <span data-ttu-id="c7b1d-265">LOH 随堆的其余部分一起收集。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-265">LOH is collected with the rest of the heap.</span></span> <span data-ttu-id="c7b1d-266">LOH 需要完整的[垃圾回收](/dotnet/standard/garbage-collection/fundamentals)或[Gen2 集合](/dotnet/standard/garbage-collection/fundamentals#generations)。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-266">LOH requires a full [garbage collection](/dotnet/standard/garbage-collection/fundamentals) or [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations).</span></span>

<span data-ttu-id="c7b1d-267">此[博客文章](https://adamsitnik.com/Array-Pool/#the-problem)简单介绍了问题：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-267">This [blog post](https://adamsitnik.com/Array-Pool/#the-problem) describes the problem succinctly:</span></span>

> <span data-ttu-id="c7b1d-268">分配大型对象时，会将其标记为第2代对象。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-268">When a large object is allocated, it’s marked as Gen 2 object.</span></span> <span data-ttu-id="c7b1d-269">对于小对象，不是0代。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-269">Not Gen 0 as for small objects.</span></span> <span data-ttu-id="c7b1d-270">后果是，如果在 LOH 中用尽内存，GC 将清除整个托管堆，而不仅是 LOH。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-270">The consequences are that if you run out of memory in LOH, GC cleans up the whole managed heap, not only LOH.</span></span> <span data-ttu-id="c7b1d-271">因此，它会清除第0代第1代和第2代，包括 LOH。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-271">So it cleans up Gen 0, Gen 1 and Gen 2 including LOH.</span></span> <span data-ttu-id="c7b1d-272">这称为完整垃圾回收，是最耗费时间的垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-272">This is called full garbage collection and is the most time-consuming garbage collection.</span></span> <span data-ttu-id="c7b1d-273">许多应用程序都可以接受。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-273">For many applications, it can be acceptable.</span></span> <span data-ttu-id="c7b1d-274">但一定不要用于高性能的 web 服务器，在这种情况下，需要少量的大内存缓冲区来处理平均 web 请求（从套接字读取、解压缩、解码 JSON & 更多）。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-274">But definitely not for high-performance web servers, where few big memory buffers are needed to handle an average web request (read from a socket, decompress, decode JSON & more).</span></span>

<span data-ttu-id="c7b1d-275">将大型请求或响应正文存储到单个 `byte[]` 或 `string`中的 Naively：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-275">Naively storing a large request or response body into a single `byte[]` or `string`:</span></span>

* <span data-ttu-id="c7b1d-276">可能会导致 LOH 中的空间快速耗尽。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-276">May result in quickly running out of space in the LOH.</span></span>
* <span data-ttu-id="c7b1d-277">可能导致应用程序出现性能问题，因为正在运行完全 Gc。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-277">May cause performance issues for the app because of full GCs running.</span></span>

## <a name="working-with-a-synchronous-data-processing-api"></a><span data-ttu-id="c7b1d-278">使用同步数据处理 API</span><span class="sxs-lookup"><span data-stu-id="c7b1d-278">Working with a synchronous data processing API</span></span>

<span data-ttu-id="c7b1d-279">使用仅支持同步读和写的序列化程序/反序列化程序（例如， [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)）时：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-279">When using a serializer/de-serializer that only supports synchronous reads and writes (for example,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span></span>

* <span data-ttu-id="c7b1d-280">将数据异步缓冲到内存中，然后将其传递给序列化程序/反序列化程序。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-280">Buffer the data into memory asynchronously before passing it into the serializer/de-serializer.</span></span>

> [!WARNING]
> <span data-ttu-id="c7b1d-281">如果请求很大，则可能导致内存不足（OOM）。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-281">If the request is large, it could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="c7b1d-282">OOM 可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-282">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="c7b1d-283">有关详细信息，请参阅本文档中的[避免将大型请求正文或响应正文读入内存](#arlb)中。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-283">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="c7b1d-284">默认情况下，ASP.NET Core 3.0 使用 <xref:System.Text.Json> 进行 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-284">ASP.NET Core 3.0 uses <xref:System.Text.Json> by default for JSON serialization.</span></span> <span data-ttu-id="c7b1d-285"><xref:System.Text.Json>：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-285"><xref:System.Text.Json>:</span></span>

* <span data-ttu-id="c7b1d-286">以异步方式读取和写入 JSON。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-286">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="c7b1d-287">针对 UTF-8 文本进行了优化。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-287">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="c7b1d-288">通常比 `Newtonsoft.Json` 性能更高。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-288">Typically higher performance than `Newtonsoft.Json`.</span></span>

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a><span data-ttu-id="c7b1d-289">不要在字段中存储 IHttpContextAccessor</span><span class="sxs-lookup"><span data-stu-id="c7b1d-289">Do not store IHttpContextAccessor.HttpContext in a field</span></span>

<span data-ttu-id="c7b1d-290">从请求线程访问时， [IHttpContextAccessor](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)将返回活动请求的 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-290">The [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) returns the `HttpContext` of the active request when accessed from the request thread.</span></span> <span data-ttu-id="c7b1d-291">`IHttpContextAccessor.HttpContext`**不**应存储在字段或变量中。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-291">The `IHttpContextAccessor.HttpContext` should **not** be stored in a field or variable.</span></span>

<span data-ttu-id="c7b1d-292">请勿**执行此操作：** 下面的示例将 `HttpContext` 存储在字段中，然后稍后尝试使用它。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-292">**Do not do this:** The following example stores the `HttpContext` in a field, and then attempts to use it later.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

<span data-ttu-id="c7b1d-293">前面的代码在构造函数中频繁捕获 null 或不正确的 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-293">The preceding code frequently captures a null or incorrect `HttpContext` in the constructor.</span></span>

<span data-ttu-id="c7b1d-294">**执行以下操作：** 下面的示例：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-294">**Do this:** The following example:</span></span>

* <span data-ttu-id="c7b1d-295">将 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 存储在字段中。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-295">Stores the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in a field.</span></span>
* <span data-ttu-id="c7b1d-296">在正确的时间使用 `HttpContext` 字段并检查 `null`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-296">Uses the `HttpContext` field at the correct time and checks for `null`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a><span data-ttu-id="c7b1d-297">不要从多个线程访问 HttpContext</span><span class="sxs-lookup"><span data-stu-id="c7b1d-297">Do not access HttpContext from multiple threads</span></span>

<span data-ttu-id="c7b1d-298">`HttpContext`*不*是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-298">`HttpContext` is *NOT* thread-safe.</span></span> <span data-ttu-id="c7b1d-299">并行访问来自多个线程的 `HttpContext` 可能会导致未定义的行为，如挂起、崩溃和数据损坏。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-299">Accessing `HttpContext` from multiple threads in parallel can result in undefined behavior such as hangs, crashes, and data corruption.</span></span>

<span data-ttu-id="c7b1d-300">请勿**执行此操作：** 下面的示例执行三个并行请求，并在传出 HTTP 请求之前和之后记录传入的请求路径。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-300">**Do not do this:** The following example makes three parallel requests and logs the incoming request path before and after the outgoing HTTP request.</span></span> <span data-ttu-id="c7b1d-301">可以从多个线程访问请求路径，可能会并行进行。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-301">The request path is accessed from multiple threads, potentially in parallel.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

<span data-ttu-id="c7b1d-302">**执行以下操作：** 下面的示例在发出三个并行请求之前复制传入请求中的所有数据。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-302">**Do this:** The following example copies all data from the incoming request before making the three parallel requests.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a><span data-ttu-id="c7b1d-303">请求完成后，不要使用 HttpContext</span><span class="sxs-lookup"><span data-stu-id="c7b1d-303">Do not use the HttpContext after the request is complete</span></span>

<span data-ttu-id="c7b1d-304">只要 ASP.NET Core 管道中存在活动 HTTP 请求，`HttpContext` 才有效。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-304">`HttpContext` is only valid as long as there is an active HTTP request in the ASP.NET Core pipeline.</span></span> <span data-ttu-id="c7b1d-305">整个 ASP.NET Core 管道是一系列执行每个请求的委托。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-305">The entire ASP.NET Core pipeline is an asynchronous chain of delegates that executes every request.</span></span> <span data-ttu-id="c7b1d-306">从此链返回的 `Task` 完成后，`HttpContext` 将被回收。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-306">When the `Task` returned from this chain completes, the `HttpContext` is recycled.</span></span>

<span data-ttu-id="c7b1d-307">请勿**执行此操作：** 下面的示例使用 `async void`，这会在达到第一个 `await` 时完成 HTTP 请求：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-307">**Do not do this:** The following example uses `async void` which makes the HTTP request complete when the first `await` is reached:</span></span>

* <span data-ttu-id="c7b1d-308">在 ASP.NET Core 应用程序中，这**始终**是一种不好的做法。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-308">Which is **ALWAYS** a bad practice in ASP.NET Core apps.</span></span>
* <span data-ttu-id="c7b1d-309">HTTP 请求完成后，访问 `HttpResponse`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-309">Accesses the `HttpResponse` after the HTTP request is complete.</span></span>
* <span data-ttu-id="c7b1d-310">崩溃进程。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-310">Crashes the process.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

<span data-ttu-id="c7b1d-311">**执行以下操作：** 下面的示例将 `Task` 返回到框架，以便在操作完成之前，不会完成 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-311">**Do this:** The following example returns a `Task` to the framework so the HTTP request doesn't complete until the action completes.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a><span data-ttu-id="c7b1d-312">不要捕获后台线程中的 HttpContext</span><span class="sxs-lookup"><span data-stu-id="c7b1d-312">Do not capture the HttpContext in background threads</span></span>

<span data-ttu-id="c7b1d-313">请勿**执行此操作：** 下面的示例演示关闭从 `Controller` 属性捕获 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-313">**Do not do this:** The following example shows a closure is capturing the `HttpContext` from the `Controller` property.</span></span> <span data-ttu-id="c7b1d-314">这是一种不好的做法，因为工作项可以：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-314">This is a bad practice because the work item could:</span></span>

* <span data-ttu-id="c7b1d-315">在请求范围之外运行。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-315">Run outside of the request scope.</span></span>
* <span data-ttu-id="c7b1d-316">尝试读取错误的 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-316">Attempt to read the wrong `HttpContext`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

<span data-ttu-id="c7b1d-317">**执行以下操作：** 下面的示例：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-317">**Do this:** The following example:</span></span>

* <span data-ttu-id="c7b1d-318">在请求过程中复制后台任务所需的数据。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-318">Copies the data required in the background task during the request.</span></span>
* <span data-ttu-id="c7b1d-319">不从控制器引用任何内容。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-319">Doesn't reference anything from the controller.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

<span data-ttu-id="c7b1d-320">应将后台任务作为托管服务实现。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-320">Background tasks should be implemented as hosted services.</span></span> <span data-ttu-id="c7b1d-321">有关详细信息，请参阅[使用托管服务的后台任务](xref:fundamentals/host/hosted-services)。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-321">For more information, see [Background tasks with hosted services](xref:fundamentals/host/hosted-services).</span></span>

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a><span data-ttu-id="c7b1d-322">不要捕获注入到后台线程控制器的服务</span><span class="sxs-lookup"><span data-stu-id="c7b1d-322">Do not capture services injected into the controllers on background threads</span></span>

<span data-ttu-id="c7b1d-323">请勿**执行此操作：** 下面的示例演示关闭从 `Controller` 操作参数捕获 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-323">**Do not do this:** The following example shows a closure is capturing the `DbContext` from the `Controller` action parameter.</span></span> <span data-ttu-id="c7b1d-324">这是一种不好的做法。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-324">This is a bad practice.</span></span>  <span data-ttu-id="c7b1d-325">工作项可以在请求范围之外运行。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-325">The work item could run outside of the request scope.</span></span> <span data-ttu-id="c7b1d-326">`ContosoDbContext` 的作用域限定为请求，导致 `ObjectDisposedException`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-326">The `ContosoDbContext` is scoped to the request, resulting in an `ObjectDisposedException`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

<span data-ttu-id="c7b1d-327">**执行以下操作：** 下面的示例：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-327">**Do this:** The following example:</span></span>

* <span data-ttu-id="c7b1d-328">注入 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 以便在后台工作项中创建作用域。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-328">Injects an <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> in order to create a scope in the background work item.</span></span> <span data-ttu-id="c7b1d-329">`IServiceScopeFactory` 为单一实例。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-329">`IServiceScopeFactory` is a singleton.</span></span>
* <span data-ttu-id="c7b1d-330">在后台线程中创建新的依赖项注入范围。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-330">Creates a new dependency injection scope in the background thread.</span></span>
* <span data-ttu-id="c7b1d-331">不从控制器引用任何内容。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-331">Doesn't reference anything from the controller.</span></span>
* <span data-ttu-id="c7b1d-332">不捕获传入请求中的 `ContosoDbContext`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-332">Doesn't capture the `ContosoDbContext` from the incoming request.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

<span data-ttu-id="c7b1d-333">以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-333">The following highlighted code:</span></span>

* <span data-ttu-id="c7b1d-334">在后台操作的生存期内创建一个范围，并从中解析服务。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-334">Creates a scope for the lifetime of the background operation and resolves services from it.</span></span>
* <span data-ttu-id="c7b1d-335">使用来自正确范围的 `ContosoDbContext`。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-335">Uses `ContosoDbContext` from the correct scope.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a><span data-ttu-id="c7b1d-336">请不要在响应正文开始后修改状态代码或标头</span><span class="sxs-lookup"><span data-stu-id="c7b1d-336">Do not modify the status code or headers after the response body has started</span></span>

<span data-ttu-id="c7b1d-337">ASP.NET Core 不会缓冲 HTTP 响应正文。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-337">ASP.NET Core does not buffer the HTTP response body.</span></span> <span data-ttu-id="c7b1d-338">第一次写入响应时：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-338">The first time the response is written:</span></span>

* <span data-ttu-id="c7b1d-339">标头将与主体块区一起发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-339">The headers are sent along with that chunk of the body to the client.</span></span>
* <span data-ttu-id="c7b1d-340">不能再更改响应标头。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-340">It's no longer possible to change response headers.</span></span>

<span data-ttu-id="c7b1d-341">请勿**执行此操作：** 以下代码在响应已启动之后尝试添加响应标头：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-341">**Do not do this:** The following code tries to add response headers after the response has already started:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

<span data-ttu-id="c7b1d-342">在前面的代码中，如果 `next()` 已写入响应，则 `context.Response.Headers["test"] = "test value";` 会引发异常。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-342">In the preceding code, `context.Response.Headers["test"] = "test value";` will throw an exception if `next()` has written to the response.</span></span>

<span data-ttu-id="c7b1d-343">**执行以下操作：** 下面的示例在修改标头之前检查 HTTP 响应是否已启动。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-343">**Do this:** The following example checks if the HTTP response has started before modifying the headers.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

<span data-ttu-id="c7b1d-344">**执行以下操作：** 下面的示例使用 `HttpResponse.OnStarting` 在将响应标头刷新到客户端之前设置标头。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-344">**Do this:** The following example uses `HttpResponse.OnStarting` to set the headers before the response headers are flushed to the client.</span></span>

<span data-ttu-id="c7b1d-345">如果检查响应是否尚未启动，则允许注册将在写入响应标头之前调用的回调。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-345">Checking if the response has not started allows registering a callback that will be invoked just before response headers are written.</span></span> <span data-ttu-id="c7b1d-346">检查响应是否尚未开始：</span><span class="sxs-lookup"><span data-stu-id="c7b1d-346">Checking if the response has not started:</span></span>

* <span data-ttu-id="c7b1d-347">提供了随时追加或重写标头的功能。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-347">Provides the ability to append or override headers just in time.</span></span>
* <span data-ttu-id="c7b1d-348">不需要了解管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-348">Doesn't require knowledge of the next middleware in the pipeline.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a><span data-ttu-id="c7b1d-349">如果已开始写入响应正文，则不调用 next （）</span><span class="sxs-lookup"><span data-stu-id="c7b1d-349">Do not call next() if you have already started writing to the response body</span></span>

<span data-ttu-id="c7b1d-350">仅当组件可以处理和操作响应时，才应调用组件。</span><span class="sxs-lookup"><span data-stu-id="c7b1d-350">Components only expect to be called if it's possible for them to handle and manipulate the response.</span></span>
