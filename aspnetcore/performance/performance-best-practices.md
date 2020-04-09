---
title: ASP.NET核心性能最佳实践
author: mjrousos
description: 提高 ASP.NET 核心应用的性能并避免常见的性能问题的提示。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977179"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="5720b-103">ASP.NET核心性能最佳实践</span><span class="sxs-lookup"><span data-stu-id="5720b-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="5720b-104">作者：[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="5720b-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="5720b-105">本文提供了ASP.NET核心的性能最佳实践指南。</span><span class="sxs-lookup"><span data-stu-id="5720b-105">This article provides guidelines for performance best practices with ASP.NET Core.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="5720b-106">主动缓存</span><span class="sxs-lookup"><span data-stu-id="5720b-106">Cache aggressively</span></span>

<span data-ttu-id="5720b-107">本文档的几个部分讨论了缓存。</span><span class="sxs-lookup"><span data-stu-id="5720b-107">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="5720b-108">有关详细信息，请参阅 <xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="5720b-108">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="understand-hot-code-paths"></a><span data-ttu-id="5720b-109">了解热代码路径</span><span class="sxs-lookup"><span data-stu-id="5720b-109">Understand hot code paths</span></span>

<span data-ttu-id="5720b-110">在本文中，*热代码路径*定义为经常调用的代码路径以及大部分执行时间发生的位置。</span><span class="sxs-lookup"><span data-stu-id="5720b-110">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="5720b-111">热代码路径通常限制应用横向扩展和性能，本文档的几个部分对此进行讨论。</span><span class="sxs-lookup"><span data-stu-id="5720b-111">Hot code paths typically limit app scale-out and performance and are discussed in several parts of this document.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="5720b-112">避免阻塞呼叫</span><span class="sxs-lookup"><span data-stu-id="5720b-112">Avoid blocking calls</span></span>

<span data-ttu-id="5720b-113">ASP.NET核心应用应设计为同时处理许多请求。</span><span class="sxs-lookup"><span data-stu-id="5720b-113">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="5720b-114">异步 API 允许小线程池通过不等待阻塞调用来处理数千个并发请求。</span><span class="sxs-lookup"><span data-stu-id="5720b-114">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="5720b-115">线程可以处理另一个请求，而不是等待长时间运行的同步任务来完成。</span><span class="sxs-lookup"><span data-stu-id="5720b-115">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="5720b-116">ASP.NET Core 应用中常见的性能问题是阻止可能是异步的调用。</span><span class="sxs-lookup"><span data-stu-id="5720b-116">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="5720b-117">许多同步阻塞调用会导致[线程池不足](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)和响应时间降低。</span><span class="sxs-lookup"><span data-stu-id="5720b-117">Many synchronous blocking calls lead to [Thread Pool starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) and degraded response times.</span></span>

<span data-ttu-id="5720b-118">**不要**：</span><span class="sxs-lookup"><span data-stu-id="5720b-118">**Do not**:</span></span>

* <span data-ttu-id="5720b-119">通过调用[Task.Wait](/dotnet/api/system.threading.tasks.task.wait)或[Task.结果](/dotnet/api/system.threading.tasks.task-1.result)来阻止异步执行。</span><span class="sxs-lookup"><span data-stu-id="5720b-119">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="5720b-120">获取公共代码路径中的锁。</span><span class="sxs-lookup"><span data-stu-id="5720b-120">Acquire locks in common code paths.</span></span> <span data-ttu-id="5720b-121">ASP.NET核心应用在构建为并行运行代码时性能最出色。</span><span class="sxs-lookup"><span data-stu-id="5720b-121">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>
* <span data-ttu-id="5720b-122">调用[任务.运行](/dotnet/api/system.threading.tasks.task.run)并立即等待它。</span><span class="sxs-lookup"><span data-stu-id="5720b-122">Call [Task.Run](/dotnet/api/system.threading.tasks.task.run) and immediately await it.</span></span> <span data-ttu-id="5720b-123">ASP.NET Core 已经在正常的线程池线程上运行应用代码，因此调用 Task.Run 只会导致不必要的线程池计划。</span><span class="sxs-lookup"><span data-stu-id="5720b-123">ASP.NET Core already runs app code on normal Thread Pool threads, so calling Task.Run only results in extra unnecessary Thread Pool scheduling.</span></span> <span data-ttu-id="5720b-124">即使计划的代码会阻止线程，Task.Run 也不会阻止这一点。</span><span class="sxs-lookup"><span data-stu-id="5720b-124">Even if the scheduled code would block a thread, Task.Run does not prevent that.</span></span>

<span data-ttu-id="5720b-125">**做**：</span><span class="sxs-lookup"><span data-stu-id="5720b-125">**Do**:</span></span>

* <span data-ttu-id="5720b-126">使[热代码路径](#understand-hot-code-paths)异步。</span><span class="sxs-lookup"><span data-stu-id="5720b-126">Make [hot code paths](#understand-hot-code-paths) asynchronous.</span></span>
* <span data-ttu-id="5720b-127">如果异步 API 可用，则异步 API 会异步调用数据访问、I/O 和长期运行的操作 API。</span><span class="sxs-lookup"><span data-stu-id="5720b-127">Call data access, I/O, and long-running operations APIs asynchronously if an asynchronous API is available.</span></span> <span data-ttu-id="5720b-128">**不要**使用[Task.Run](/dotnet/api/system.threading.tasks.task.run)使同步 API 异步。</span><span class="sxs-lookup"><span data-stu-id="5720b-128">Do **not** use [Task.Run](/dotnet/api/system.threading.tasks.task.run) to make a synchronus API asynchronous.</span></span>
* <span data-ttu-id="5720b-129">使控制器/Razor 页面操作异步。</span><span class="sxs-lookup"><span data-stu-id="5720b-129">Make controller/Razor Page actions asynchronous.</span></span> <span data-ttu-id="5720b-130">整个调用堆栈是异步的，以便从[异步/等待](/dotnet/csharp/programming-guide/concepts/async/)模式中获益。</span><span class="sxs-lookup"><span data-stu-id="5720b-130">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="5720b-131">探查器（如[PerfView）](https://github.com/Microsoft/perfview)可用于查找经常添加到[线程池的线程](/windows/desktop/procthread/thread-pools)。</span><span class="sxs-lookup"><span data-stu-id="5720b-131">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="5720b-132">该`Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`事件指示添加到线程池的线程。</span><span class="sxs-lookup"><span data-stu-id="5720b-132">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="5720b-133">最小化大型对象分配</span><span class="sxs-lookup"><span data-stu-id="5720b-133">Minimize large object allocations</span></span>

<span data-ttu-id="5720b-134">[.NET 核心垃圾回收器](/dotnet/standard/garbage-collection/)在 ASP.NET 核心应用中自动管理内存的分配和释放。</span><span class="sxs-lookup"><span data-stu-id="5720b-134">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="5720b-135">自动垃圾回收通常意味着开发人员无需担心如何或何时释放内存。</span><span class="sxs-lookup"><span data-stu-id="5720b-135">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="5720b-136">但是，清理未引用的对象需要 CPU 时间，因此开发人员应尽量减少[在热代码路径](#understand-hot-code-paths)中分配对象。</span><span class="sxs-lookup"><span data-stu-id="5720b-136">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#understand-hot-code-paths).</span></span> <span data-ttu-id="5720b-137">垃圾回收对于大型对象（> 85 K 字节）特别昂贵。</span><span class="sxs-lookup"><span data-stu-id="5720b-137">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="5720b-138">大型对象存储在[大型对象堆](/dotnet/standard/garbage-collection/large-object-heap)上，需要完整（第 2 代）垃圾回收来清理。</span><span class="sxs-lookup"><span data-stu-id="5720b-138">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="5720b-139">与第 0 代和第 1 代集合不同，第 2 代集合需要暂时暂停应用执行。</span><span class="sxs-lookup"><span data-stu-id="5720b-139">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="5720b-140">频繁分配和取消分配大型对象可能会导致性能不一致。</span><span class="sxs-lookup"><span data-stu-id="5720b-140">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="5720b-141">建议：</span><span class="sxs-lookup"><span data-stu-id="5720b-141">Recommendations:</span></span>

* <span data-ttu-id="5720b-142">**请考虑**缓存经常使用的大型对象。</span><span class="sxs-lookup"><span data-stu-id="5720b-142">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="5720b-143">缓存大型对象可防止昂贵的分配。</span><span class="sxs-lookup"><span data-stu-id="5720b-143">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="5720b-144">使用[ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1)存储大型数组来**执行**池缓冲区。</span><span class="sxs-lookup"><span data-stu-id="5720b-144">**Do** pool buffers by using an [ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="5720b-145">**不要**在[热代码路径](#understand-hot-code-paths)上分配许多短寿命的大对象。</span><span class="sxs-lookup"><span data-stu-id="5720b-145">**Do not** allocate many, short-lived large objects on [hot code paths](#understand-hot-code-paths).</span></span>

<span data-ttu-id="5720b-146">可以通过查看[PerfView](https://github.com/Microsoft/perfview)中的垃圾回收 （GC） 统计信息并检查内存问题（如上述问题）来诊断：</span><span class="sxs-lookup"><span data-stu-id="5720b-146">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="5720b-147">垃圾回收暂停时间。</span><span class="sxs-lookup"><span data-stu-id="5720b-147">Garbage collection pause time.</span></span>
* <span data-ttu-id="5720b-148">在垃圾回收中花费的处理器时间的百分比。</span><span class="sxs-lookup"><span data-stu-id="5720b-148">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="5720b-149">有多少垃圾回收是生成 0、1 和 2。</span><span class="sxs-lookup"><span data-stu-id="5720b-149">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="5720b-150">有关详细信息，请参阅[垃圾回收和性能](/dotnet/standard/garbage-collection/performance)。</span><span class="sxs-lookup"><span data-stu-id="5720b-150">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access-and-io"></a><span data-ttu-id="5720b-151">优化数据访问和 I/O</span><span class="sxs-lookup"><span data-stu-id="5720b-151">Optimize data access and I/O</span></span>

<span data-ttu-id="5720b-152">与数据存储和其他远程服务的交互通常是ASP.NET核心应用中最慢的部分。</span><span class="sxs-lookup"><span data-stu-id="5720b-152">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="5720b-153">高效读取和写入数据对于良好的性能至关重要。</span><span class="sxs-lookup"><span data-stu-id="5720b-153">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="5720b-154">建议：</span><span class="sxs-lookup"><span data-stu-id="5720b-154">Recommendations:</span></span>

* <span data-ttu-id="5720b-155">**请**异步调用所有数据访问 API。</span><span class="sxs-lookup"><span data-stu-id="5720b-155">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="5720b-156">**不要**检索超过所需数据的数据。</span><span class="sxs-lookup"><span data-stu-id="5720b-156">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="5720b-157">编写查询以仅返回当前 HTTP 请求所需的数据。</span><span class="sxs-lookup"><span data-stu-id="5720b-157">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="5720b-158">**如果**可以接受稍微过时的数据，请考虑缓存从数据库或远程服务检索的频繁访问的数据。</span><span class="sxs-lookup"><span data-stu-id="5720b-158">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="5720b-159">根据方案，使用[内存缓存](xref:performance/caching/memory)或[分布式缓存](xref:performance/caching/distributed)。</span><span class="sxs-lookup"><span data-stu-id="5720b-159">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="5720b-160">有关详细信息，请参阅 <xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="5720b-160">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="5720b-161">**尽量减少**网络往返。</span><span class="sxs-lookup"><span data-stu-id="5720b-161">**Do** minimize network round trips.</span></span> <span data-ttu-id="5720b-162">目标是在单个调用中检索所需的数据，而不是多个调用。</span><span class="sxs-lookup"><span data-stu-id="5720b-162">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="5720b-163">出于只读目的访问数据时，**请**使用实体框架核心中的[不跟踪查询](/ef/core/querying/tracking#no-tracking-queries)。</span><span class="sxs-lookup"><span data-stu-id="5720b-163">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="5720b-164">EF Core 可以更有效地返回无跟踪查询的结果。</span><span class="sxs-lookup"><span data-stu-id="5720b-164">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="5720b-165">**执行**筛选和聚合 LINQ 查询（`.Where`例如`.Select`，使用`.Sum`或 语句），以便由数据库执行筛选。</span><span class="sxs-lookup"><span data-stu-id="5720b-165">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="5720b-166">**一应考虑**EF Core 解析客户端上的一些查询运算符，这可能导致查询执行效率低下。</span><span class="sxs-lookup"><span data-stu-id="5720b-166">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="5720b-167">有关详细信息，请参阅[客户端评估性能问题](/ef/core/querying/client-eval#client-evaluation-performance-issues)。</span><span class="sxs-lookup"><span data-stu-id="5720b-167">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="5720b-168">**不要**对集合使用投影查询，这可能导致执行"N + 1"SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="5720b-168">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="5720b-169">有关详细信息，请参阅[优化相关子查询](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)。</span><span class="sxs-lookup"><span data-stu-id="5720b-169">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="5720b-170">有关可能提高大规模应用中性能的方法，请参阅[EF 高性能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)：</span><span class="sxs-lookup"><span data-stu-id="5720b-170">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="5720b-171">DbContext 池</span><span class="sxs-lookup"><span data-stu-id="5720b-171">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="5720b-172">显式编译的查询</span><span class="sxs-lookup"><span data-stu-id="5720b-172">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="5720b-173">我们建议在提交代码库之前测量上述高性能方法的影响。</span><span class="sxs-lookup"><span data-stu-id="5720b-173">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="5720b-174">编译查询的额外复杂性可能无法证明性能改进的合理性。</span><span class="sxs-lookup"><span data-stu-id="5720b-174">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="5720b-175">可以通过查看使用[应用程序见解](/azure/application-insights/app-insights-overview)或分析工具访问数据所花费的时间来检测查询问题。</span><span class="sxs-lookup"><span data-stu-id="5720b-175">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="5720b-176">大多数数据库还提供有关频繁执行的查询的统计信息。</span><span class="sxs-lookup"><span data-stu-id="5720b-176">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="5720b-177">使用 HttpClientFactory 池 HTTP 连接</span><span class="sxs-lookup"><span data-stu-id="5720b-177">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="5720b-178">尽管[HttpClient](/dotnet/api/system.net.http.httpclient) `IDisposable`实现了该接口，但它旨在重用。</span><span class="sxs-lookup"><span data-stu-id="5720b-178">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="5720b-179">关闭`HttpClient`的实例使套接字处于`TIME_WAIT`状态短时间打开。</span><span class="sxs-lookup"><span data-stu-id="5720b-179">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="5720b-180">如果经常使用创建和释放`HttpClient`对象的代码路径，则应用可能会耗尽可用的套接字。</span><span class="sxs-lookup"><span data-stu-id="5720b-180">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="5720b-181">[httpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)在 ASP.NET 核心 2.1 中引入，作为此问题的解决方案。</span><span class="sxs-lookup"><span data-stu-id="5720b-181">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="5720b-182">它处理池 HTTP 连接以优化性能和可靠性。</span><span class="sxs-lookup"><span data-stu-id="5720b-182">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="5720b-183">建议：</span><span class="sxs-lookup"><span data-stu-id="5720b-183">Recommendations:</span></span>

* <span data-ttu-id="5720b-184">**不要**直接创建和处置`HttpClient`实例。</span><span class="sxs-lookup"><span data-stu-id="5720b-184">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="5720b-185">**请使用** [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)检索`HttpClient`实例。</span><span class="sxs-lookup"><span data-stu-id="5720b-185">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="5720b-186">有关详细信息，请参阅[使用 HttpClientFactory 实现可复原的 HTTP 请求](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)。</span><span class="sxs-lookup"><span data-stu-id="5720b-186">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="5720b-187">保持常见代码路径快速</span><span class="sxs-lookup"><span data-stu-id="5720b-187">Keep common code paths fast</span></span>

<span data-ttu-id="5720b-188">您希望所有代码都快速。</span><span class="sxs-lookup"><span data-stu-id="5720b-188">You want all of your code to be fast.</span></span> <span data-ttu-id="5720b-189">经常调用的代码路径是优化的最重要路径。</span><span class="sxs-lookup"><span data-stu-id="5720b-189">Frequently-called code paths are the most critical to optimize.</span></span> <span data-ttu-id="5720b-190">其中包括:</span><span class="sxs-lookup"><span data-stu-id="5720b-190">These include:</span></span>

* <span data-ttu-id="5720b-191">应用请求处理管道中的中间件组件，尤其是中间件在管道中早期运行。</span><span class="sxs-lookup"><span data-stu-id="5720b-191">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="5720b-192">这些组件对性能有很大影响。</span><span class="sxs-lookup"><span data-stu-id="5720b-192">These components have a large impact on performance.</span></span>
* <span data-ttu-id="5720b-193">为每个请求执行的代码或每个请求多次执行的代码。</span><span class="sxs-lookup"><span data-stu-id="5720b-193">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="5720b-194">例如，自定义日志记录、授权处理程序或暂态服务的初始化。</span><span class="sxs-lookup"><span data-stu-id="5720b-194">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="5720b-195">建议：</span><span class="sxs-lookup"><span data-stu-id="5720b-195">Recommendations:</span></span>

* <span data-ttu-id="5720b-196">**请勿**将自定义中间件组件用于长时间运行的任务。</span><span class="sxs-lookup"><span data-stu-id="5720b-196">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="5720b-197">**请使用**性能分析工具（如[可视化工作室诊断工具](/visualstudio/profiling/profiling-feature-tour)或[PerfView）](https://github.com/Microsoft/perfview)来标识[热代码路径](#understand-hot-code-paths)。</span><span class="sxs-lookup"><span data-stu-id="5720b-197">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#understand-hot-code-paths).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="5720b-198">在 HTTP 请求之外完成长时间运行的任务</span><span class="sxs-lookup"><span data-stu-id="5720b-198">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="5720b-199">对ASP.NET Core 应用的大多数请求都可以由调用必要服务的控制器或页面模型处理并返回 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="5720b-199">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="5720b-200">对于某些涉及长时间运行的任务的请求，最好使整个请求-响应过程异步。</span><span class="sxs-lookup"><span data-stu-id="5720b-200">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="5720b-201">建议：</span><span class="sxs-lookup"><span data-stu-id="5720b-201">Recommendations:</span></span>

* <span data-ttu-id="5720b-202">**不要**等待长时间运行的任务作为普通 HTTP 请求处理的一部分完成。</span><span class="sxs-lookup"><span data-stu-id="5720b-202">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="5720b-203">**请考虑**使用[后台服务](xref:fundamentals/host/hosted-services)处理长时间运行的请求，或者使用[Azure 函数](/azure/azure-functions/)处理进程外的请求。</span><span class="sxs-lookup"><span data-stu-id="5720b-203">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="5720b-204">在进程外完成工作对 CPU 密集型任务特别有利。</span><span class="sxs-lookup"><span data-stu-id="5720b-204">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="5720b-205">**请使用**实时通信选项（如[SignalR](xref:signalr/introduction)）以异步方式与客户端通信。</span><span class="sxs-lookup"><span data-stu-id="5720b-205">**Do** use real-time communication options, such as [SignalR](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="5720b-206">将客户资产进行最小化</span><span class="sxs-lookup"><span data-stu-id="5720b-206">Minify client assets</span></span>

<span data-ttu-id="5720b-207">ASP.NET具有复杂前端的核心应用经常提供许多 JavaScript、CSS 或图像文件。</span><span class="sxs-lookup"><span data-stu-id="5720b-207">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="5720b-208">初始加载请求的性能可以通过：</span><span class="sxs-lookup"><span data-stu-id="5720b-208">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="5720b-209">捆绑，它将多个文件合并为一个文件。</span><span class="sxs-lookup"><span data-stu-id="5720b-209">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="5720b-210">明量化，通过删除空格和注释来减小文件的大小。</span><span class="sxs-lookup"><span data-stu-id="5720b-210">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="5720b-211">建议：</span><span class="sxs-lookup"><span data-stu-id="5720b-211">Recommendations:</span></span>

* <span data-ttu-id="5720b-212">**使用**ASP.NET Core 的[内置支持](xref:client-side/bundling-and-minification)来捆绑和美化客户资产。</span><span class="sxs-lookup"><span data-stu-id="5720b-212">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="5720b-213">**请考虑**其他第三方工具，如[Webpack，](https://webpack.js.org/)用于复杂的客户端资产管理。</span><span class="sxs-lookup"><span data-stu-id="5720b-213">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="5720b-214">压缩响应</span><span class="sxs-lookup"><span data-stu-id="5720b-214">Compress responses</span></span>

 <span data-ttu-id="5720b-215">减小响应大小通常会增加应用程序的响应能力，通常显著。</span><span class="sxs-lookup"><span data-stu-id="5720b-215">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="5720b-216">减少有效负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="5720b-216">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="5720b-217">有关详细信息，请参阅[响应压缩](xref:performance/response-compression)。</span><span class="sxs-lookup"><span data-stu-id="5720b-217">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="5720b-218">使用最新的ASP.NET核心版本</span><span class="sxs-lookup"><span data-stu-id="5720b-218">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="5720b-219">每个新版本ASP.NET核心包括性能改进。</span><span class="sxs-lookup"><span data-stu-id="5720b-219">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="5720b-220">.NET Core 和 ASP.NET 核心中的优化意味着较新版本的性能通常优于旧版本。</span><span class="sxs-lookup"><span data-stu-id="5720b-220">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="5720b-221">例如，.NET Core 2.1 添加了对编译正则表达式的支持，并从[span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx)中获益。</span><span class="sxs-lookup"><span data-stu-id="5720b-221">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx).</span></span> <span data-ttu-id="5720b-222">ASP.NET核心 2.2 添加了对 HTTP/2 的支持。</span><span class="sxs-lookup"><span data-stu-id="5720b-222">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="5720b-223">[ASP.NET Core 3.0 添加了许多改进](xref:aspnetcore-3.0)，以减少内存使用并提高吞吐量。</span><span class="sxs-lookup"><span data-stu-id="5720b-223">[ASP.NET Core 3.0 adds many improvements](xref:aspnetcore-3.0) that reduce memory usage and improve throughput.</span></span> <span data-ttu-id="5720b-224">如果性能是重中之重，请考虑升级到当前版本的 ASP.NET 酷睿。</span><span class="sxs-lookup"><span data-stu-id="5720b-224">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

## <a name="minimize-exceptions"></a><span data-ttu-id="5720b-225">最小化异常</span><span class="sxs-lookup"><span data-stu-id="5720b-225">Minimize exceptions</span></span>

<span data-ttu-id="5720b-226">异常应该很少见。</span><span class="sxs-lookup"><span data-stu-id="5720b-226">Exceptions should be rare.</span></span> <span data-ttu-id="5720b-227">与其他代码流模式相比，引发和捕获异常的速度很慢。</span><span class="sxs-lookup"><span data-stu-id="5720b-227">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="5720b-228">因此，不应使用异常来控制正常程序流。</span><span class="sxs-lookup"><span data-stu-id="5720b-228">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="5720b-229">建议：</span><span class="sxs-lookup"><span data-stu-id="5720b-229">Recommendations:</span></span>

* <span data-ttu-id="5720b-230">**请勿**使用引发或捕获异常作为正常程序流的手段，尤其是在[热代码路径](#understand-hot-code-paths)中。</span><span class="sxs-lookup"><span data-stu-id="5720b-230">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#understand-hot-code-paths).</span></span>
* <span data-ttu-id="5720b-231">**在**应用中是否包括逻辑，以检测和处理会导致异常的条件。</span><span class="sxs-lookup"><span data-stu-id="5720b-231">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="5720b-232">对于异常或意外的情况，**一应**引发或捕获异常。</span><span class="sxs-lookup"><span data-stu-id="5720b-232">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="5720b-233">应用诊断工具（如应用程序见解）可帮助识别应用中可能影响性能的常见异常。</span><span class="sxs-lookup"><span data-stu-id="5720b-233">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>

## <a name="performance-and-reliability"></a><span data-ttu-id="5720b-234">性能和可靠性</span><span class="sxs-lookup"><span data-stu-id="5720b-234">Performance and reliability</span></span>

<span data-ttu-id="5720b-235">以下各节提供性能提示和已知可靠性问题和解决方案。</span><span class="sxs-lookup"><span data-stu-id="5720b-235">The following sections provide performance tips and known reliability problems and solutions.</span></span>

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a><span data-ttu-id="5720b-236">避免在 HttpRequest/HttpResponse 正文上同步读取或写入</span><span class="sxs-lookup"><span data-stu-id="5720b-236">Avoid synchronous read or write on HttpRequest/HttpResponse body</span></span>

<span data-ttu-id="5720b-237">ASP.NET核心中的所有 I/O 都是异步的。</span><span class="sxs-lookup"><span data-stu-id="5720b-237">All I/O in ASP.NET Core is asynchronous.</span></span> <span data-ttu-id="5720b-238">服务器实现`Stream`接口，该接口具有同步和异步重载。</span><span class="sxs-lookup"><span data-stu-id="5720b-238">Servers implement the `Stream` interface, which has both synchronous and asynchronous overloads.</span></span> <span data-ttu-id="5720b-239">应首选异步线程以避免阻塞线程池线程。</span><span class="sxs-lookup"><span data-stu-id="5720b-239">The asynchronous ones should be preferred to avoid blocking thread pool threads.</span></span> <span data-ttu-id="5720b-240">阻塞线程可能导致线程池不足。</span><span class="sxs-lookup"><span data-stu-id="5720b-240">Blocking threads can lead to thread pool starvation.</span></span>

<span data-ttu-id="5720b-241">**不要这样做：** 下面的示例使用 。 <xref:System.IO.StreamReader.ReadToEnd*></span><span class="sxs-lookup"><span data-stu-id="5720b-241">**Do not do this:** The following example uses the <xref:System.IO.StreamReader.ReadToEnd*>.</span></span> <span data-ttu-id="5720b-242">它阻止当前线程等待结果。</span><span class="sxs-lookup"><span data-stu-id="5720b-242">It blocks the current thread to wait for the result.</span></span> <span data-ttu-id="5720b-243">这是[一个通过异步同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)的示例。</span><span class="sxs-lookup"><span data-stu-id="5720b-243">This is an example of [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

<span data-ttu-id="5720b-244">在前面的代码中，`Get`同步将整个 HTTP 请求正文读取到内存中。</span><span class="sxs-lookup"><span data-stu-id="5720b-244">In the preceding code, `Get` synchronously reads the entire HTTP request body into memory.</span></span> <span data-ttu-id="5720b-245">如果客户端正在缓慢上载，则应用正在通过异步进行同步。</span><span class="sxs-lookup"><span data-stu-id="5720b-245">If the client is slowly uploading, the app is doing sync over async.</span></span> <span data-ttu-id="5720b-246">应用程序会同步，因为 Kestrel**不支持**同步读取。</span><span class="sxs-lookup"><span data-stu-id="5720b-246">The app does sync over async because Kestrel does **NOT** support synchronous reads.</span></span>

<span data-ttu-id="5720b-247">**执行此操作：** 下面的示例使用<xref:System.IO.StreamReader.ReadToEndAsync*>并且不会在读取时阻止线程。</span><span class="sxs-lookup"><span data-stu-id="5720b-247">**Do this:** The following example uses <xref:System.IO.StreamReader.ReadToEndAsync*> and does not block the thread while reading.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

<span data-ttu-id="5720b-248">前面的代码异步地将整个 HTTP 请求正文读取到内存中。</span><span class="sxs-lookup"><span data-stu-id="5720b-248">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="5720b-249">如果请求很大，将整个 HTTP 请求正文读取到内存中可能会导致内存不足 （OOM） 条件。</span><span class="sxs-lookup"><span data-stu-id="5720b-249">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="5720b-250">OOM 可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="5720b-250">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="5720b-251">有关详细信息，请参阅本文档中[避免将大型请求正文或响应正文读取到内存](#arlb)中。</span><span class="sxs-lookup"><span data-stu-id="5720b-251">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="5720b-252">**执行此操作：** 以下示例使用非缓冲请求正文完全异步：</span><span class="sxs-lookup"><span data-stu-id="5720b-252">**Do this:** The following example is fully asynchronous using a non buffered request body:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

<span data-ttu-id="5720b-253">前面的代码异步将请求正文序列化为 C# 对象。</span><span class="sxs-lookup"><span data-stu-id="5720b-253">The preceding code asynchronously de-serializes the request body into a C# object.</span></span>

## <a name="prefer-readformasync-over-requestform"></a><span data-ttu-id="5720b-254">首选阅读形式同步而不是请求.窗体</span><span class="sxs-lookup"><span data-stu-id="5720b-254">Prefer ReadFormAsync over Request.Form</span></span>

<span data-ttu-id="5720b-255">使用 `HttpContext.Request.ReadFormAsync` 而非 `HttpContext.Request.Form`。</span><span class="sxs-lookup"><span data-stu-id="5720b-255">Use `HttpContext.Request.ReadFormAsync` instead of `HttpContext.Request.Form`.</span></span>
<span data-ttu-id="5720b-256">`HttpContext.Request.Form`只能在以下条件下安全读取：</span><span class="sxs-lookup"><span data-stu-id="5720b-256">`HttpContext.Request.Form` can be safely read only with the following conditions:</span></span>

* <span data-ttu-id="5720b-257">表单已通过调用 和`ReadFormAsync`</span><span class="sxs-lookup"><span data-stu-id="5720b-257">The form has been read by a call to `ReadFormAsync`, and</span></span>
* <span data-ttu-id="5720b-258">正在使用`HttpContext.Request.Form`</span><span class="sxs-lookup"><span data-stu-id="5720b-258">The cached form value is being read using `HttpContext.Request.Form`</span></span>

<span data-ttu-id="5720b-259">**不要这样做：** 下面的示例使用`HttpContext.Request.Form`。</span><span class="sxs-lookup"><span data-stu-id="5720b-259">**Do not do this:** The following example uses `HttpContext.Request.Form`.</span></span>  <span data-ttu-id="5720b-260">`HttpContext.Request.Form`[使用异步同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)，并可能导致线程池不足。</span><span class="sxs-lookup"><span data-stu-id="5720b-260">`HttpContext.Request.Form` uses [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) and can lead to thread pool starvation.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

<span data-ttu-id="5720b-261">**执行此操作：** 下面的示例用于`HttpContext.Request.ReadFormAsync`异步读取窗体正文。</span><span class="sxs-lookup"><span data-stu-id="5720b-261">**Do this:** The following example uses `HttpContext.Request.ReadFormAsync` to read the form body asynchronously.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a><span data-ttu-id="5720b-262">避免将大型请求正文或响应正文读取到内存中</span><span class="sxs-lookup"><span data-stu-id="5720b-262">Avoid reading large request bodies or response bodies into memory</span></span>

<span data-ttu-id="5720b-263">在 .NET 中，每个大于 85 KB 的对象分配最终都位于大型对象堆[（LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)） 中。</span><span class="sxs-lookup"><span data-stu-id="5720b-263">In .NET, every object allocation greater than 85 KB ends up in the large object heap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)).</span></span> <span data-ttu-id="5720b-264">大型对象在两个方面非常昂贵：</span><span class="sxs-lookup"><span data-stu-id="5720b-264">Large objects are expensive in two ways:</span></span>

* <span data-ttu-id="5720b-265">分配成本很高，因为必须清除新分配的大型对象的内存。</span><span class="sxs-lookup"><span data-stu-id="5720b-265">The allocation cost is high because the memory for a newly allocated large object has to be cleared.</span></span> <span data-ttu-id="5720b-266">CLR 保证清除所有新分配对象的内存。</span><span class="sxs-lookup"><span data-stu-id="5720b-266">The CLR guarantees that memory for all newly allocated objects is cleared.</span></span>
* <span data-ttu-id="5720b-267">LOH 与堆的其余部分一起收集。</span><span class="sxs-lookup"><span data-stu-id="5720b-267">LOH is collected with the rest of the heap.</span></span> <span data-ttu-id="5720b-268">LOH 需要完整的[垃圾回收](/dotnet/standard/garbage-collection/fundamentals)或[第 2 代收集](/dotnet/standard/garbage-collection/fundamentals#generations)。</span><span class="sxs-lookup"><span data-stu-id="5720b-268">LOH requires a full [garbage collection](/dotnet/standard/garbage-collection/fundamentals) or [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations).</span></span>

<span data-ttu-id="5720b-269">这个[博客文章](https://adamsitnik.com/Array-Pool/#the-problem)简明扼要地描述了这个问题：</span><span class="sxs-lookup"><span data-stu-id="5720b-269">This [blog post](https://adamsitnik.com/Array-Pool/#the-problem) describes the problem succinctly:</span></span>

> <span data-ttu-id="5720b-270">分配大型对象时，将将其标记为第 2 代对象。</span><span class="sxs-lookup"><span data-stu-id="5720b-270">When a large object is allocated, it's marked as Gen 2 object.</span></span> <span data-ttu-id="5720b-271">对于小对象，不是第 0 代。</span><span class="sxs-lookup"><span data-stu-id="5720b-271">Not Gen 0 as for small objects.</span></span> <span data-ttu-id="5720b-272">其后果是，如果内存在 LOH 中耗尽，GC 将清理整个托管堆，而不仅仅是 LOH。</span><span class="sxs-lookup"><span data-stu-id="5720b-272">The consequences are that if you run out of memory in LOH, GC cleans up the whole managed heap, not only LOH.</span></span> <span data-ttu-id="5720b-273">因此，它清理第 0 代、第 1 代和第 2 代（包括 LOH）。</span><span class="sxs-lookup"><span data-stu-id="5720b-273">So it cleans up Gen 0, Gen 1 and Gen 2 including LOH.</span></span> <span data-ttu-id="5720b-274">这称为完全垃圾回收，是最耗时的垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="5720b-274">This is called full garbage collection and is the most time-consuming garbage collection.</span></span> <span data-ttu-id="5720b-275">对于许多应用程序，这是可以接受的。</span><span class="sxs-lookup"><span data-stu-id="5720b-275">For many applications, it can be acceptable.</span></span> <span data-ttu-id="5720b-276">但绝对不是高性能 Web 服务器，因为处理普通 Web 请求只需要很少大内存缓冲区（从套接字读取、解压缩、解码 JSON &更多）。</span><span class="sxs-lookup"><span data-stu-id="5720b-276">But definitely not for high-performance web servers, where few big memory buffers are needed to handle an average web request (read from a socket, decompress, decode JSON & more).</span></span>

<span data-ttu-id="5720b-277">天真地将大型请求或响应正文存储到单个`byte[]`或`string`：</span><span class="sxs-lookup"><span data-stu-id="5720b-277">Naively storing a large request or response body into a single `byte[]` or `string`:</span></span>

* <span data-ttu-id="5720b-278">可能导致 LOH 中空间迅速耗尽。</span><span class="sxs-lookup"><span data-stu-id="5720b-278">May result in quickly running out of space in the LOH.</span></span>
* <span data-ttu-id="5720b-279">由于完全运行了 DC，可能会导致应用的性能问题。</span><span class="sxs-lookup"><span data-stu-id="5720b-279">May cause performance issues for the app because of full GCs running.</span></span>

## <a name="working-with-a-synchronous-data-processing-api"></a><span data-ttu-id="5720b-280">使用同步数据处理 API</span><span class="sxs-lookup"><span data-stu-id="5720b-280">Working with a synchronous data processing API</span></span>

<span data-ttu-id="5720b-281">使用仅支持同步读取和写入的序列化器/去序列化器时（例如[，JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)）：</span><span class="sxs-lookup"><span data-stu-id="5720b-281">When using a serializer/de-serializer that only supports synchronous reads and writes (for example,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span></span>

* <span data-ttu-id="5720b-282">将数据异步缓冲到内存中，然后再将其传递到序列化器/去序列化器中。</span><span class="sxs-lookup"><span data-stu-id="5720b-282">Buffer the data into memory asynchronously before passing it into the serializer/de-serializer.</span></span>

> [!WARNING]
> <span data-ttu-id="5720b-283">如果请求很大，则可能导致内存不足 （OOM） 条件。</span><span class="sxs-lookup"><span data-stu-id="5720b-283">If the request is large, it could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="5720b-284">OOM 可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="5720b-284">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="5720b-285">有关详细信息，请参阅本文档中[避免将大型请求正文或响应正文读取到内存](#arlb)中。</span><span class="sxs-lookup"><span data-stu-id="5720b-285">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="5720b-286">默认情况下，ASP.NET核心 3.0 用于<xref:System.Text.Json>JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="5720b-286">ASP.NET Core 3.0 uses <xref:System.Text.Json> by default for JSON serialization.</span></span> <span data-ttu-id="5720b-287"><xref:System.Text.Json>:</span><span class="sxs-lookup"><span data-stu-id="5720b-287"><xref:System.Text.Json>:</span></span>

* <span data-ttu-id="5720b-288">以异步方式读取和写入 JSON。</span><span class="sxs-lookup"><span data-stu-id="5720b-288">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="5720b-289">针对 UTF-8 文本进行了优化。</span><span class="sxs-lookup"><span data-stu-id="5720b-289">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="5720b-290">通常比 `Newtonsoft.Json` 性能更高。</span><span class="sxs-lookup"><span data-stu-id="5720b-290">Typically higher performance than `Newtonsoft.Json`.</span></span>

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a><span data-ttu-id="5720b-291">不要将 IHttpContextAccessor.httpContext 存储在字段中</span><span class="sxs-lookup"><span data-stu-id="5720b-291">Do not store IHttpContextAccessor.HttpContext in a field</span></span>

<span data-ttu-id="5720b-292">从请求线程访问时[，IHttpContextAccessor.httpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)`HttpContext`返回活动请求。</span><span class="sxs-lookup"><span data-stu-id="5720b-292">The [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) returns the `HttpContext` of the active request when accessed from the request thread.</span></span> <span data-ttu-id="5720b-293">`IHttpContextAccessor.HttpContext`**不应**存储在字段或变量中。</span><span class="sxs-lookup"><span data-stu-id="5720b-293">The `IHttpContextAccessor.HttpContext` should **not** be stored in a field or variable.</span></span>

<span data-ttu-id="5720b-294">**不要这样做：** 下面的示例将 存储在`HttpContext`字段中，然后尝试稍后使用它。</span><span class="sxs-lookup"><span data-stu-id="5720b-294">**Do not do this:** The following example stores the `HttpContext` in a field and then attempts to use it later.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

<span data-ttu-id="5720b-295">前面的代码经常在构造函数中捕获空或不正确`HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="5720b-295">The preceding code frequently captures a null or incorrect `HttpContext` in the constructor.</span></span>

<span data-ttu-id="5720b-296">**执行此操作：** 以下示例：</span><span class="sxs-lookup"><span data-stu-id="5720b-296">**Do this:** The following example:</span></span>

* <span data-ttu-id="5720b-297">将<xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>存储在字段中。</span><span class="sxs-lookup"><span data-stu-id="5720b-297">Stores the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in a field.</span></span>
* <span data-ttu-id="5720b-298">在正确的`HttpContext`时间使用该字段并检查`null`。</span><span class="sxs-lookup"><span data-stu-id="5720b-298">Uses the `HttpContext` field at the correct time and checks for `null`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a><span data-ttu-id="5720b-299">不要从多个线程访问 HttpContext</span><span class="sxs-lookup"><span data-stu-id="5720b-299">Do not access HttpContext from multiple threads</span></span>

<span data-ttu-id="5720b-300">`HttpContext`*不是*线程安全的。</span><span class="sxs-lookup"><span data-stu-id="5720b-300">`HttpContext` is *NOT* thread-safe.</span></span> <span data-ttu-id="5720b-301">并行`HttpContext`从多个线程访问可能会导致未定义的行为，如挂起、崩溃和数据损坏。</span><span class="sxs-lookup"><span data-stu-id="5720b-301">Accessing `HttpContext` from multiple threads in parallel can result in undefined behavior such as hangs, crashes, and data corruption.</span></span>

<span data-ttu-id="5720b-302">**不要这样做：** 下面的示例发出三个并行请求，并在传出 HTTP 请求之前和之后记录传入的请求路径。</span><span class="sxs-lookup"><span data-stu-id="5720b-302">**Do not do this:** The following example makes three parallel requests and logs the incoming request path before and after the outgoing HTTP request.</span></span> <span data-ttu-id="5720b-303">请求路径从多个线程访问，可能并行访问。</span><span class="sxs-lookup"><span data-stu-id="5720b-303">The request path is accessed from multiple threads, potentially in parallel.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

<span data-ttu-id="5720b-304">**执行此操作：** 以下示例在发出三个并行请求之前复制来自传入请求的所有数据。</span><span class="sxs-lookup"><span data-stu-id="5720b-304">**Do this:** The following example copies all data from the incoming request before making the three parallel requests.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a><span data-ttu-id="5720b-305">请求完成后不要使用 HttpContext</span><span class="sxs-lookup"><span data-stu-id="5720b-305">Do not use the HttpContext after the request is complete</span></span>

<span data-ttu-id="5720b-306">`HttpContext`仅当ASP.NET核心管道中有活动 HTTP 请求时，才有效。</span><span class="sxs-lookup"><span data-stu-id="5720b-306">`HttpContext` is only valid as long as there is an active HTTP request in the ASP.NET Core pipeline.</span></span> <span data-ttu-id="5720b-307">整个ASP.NET核心管道是执行每个请求的异步委托链。</span><span class="sxs-lookup"><span data-stu-id="5720b-307">The entire ASP.NET Core pipeline is an asynchronous chain of delegates that executes every request.</span></span> <span data-ttu-id="5720b-308">当`Task`从此链返回完成时，将回收`HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="5720b-308">When the `Task` returned from this chain completes, the `HttpContext` is recycled.</span></span>

<span data-ttu-id="5720b-309">**不要这样做：** 以下示例使用`async void`使 HTTP 请求在到达第一个`await`请求时完成：</span><span class="sxs-lookup"><span data-stu-id="5720b-309">**Do not do this:** The following example uses `async void` which makes the HTTP request complete when the first `await` is reached:</span></span>

* <span data-ttu-id="5720b-310">这在ASP.NET核心应用程序中**总是**一个坏的做法。</span><span class="sxs-lookup"><span data-stu-id="5720b-310">Which is **ALWAYS** a bad practice in ASP.NET Core apps.</span></span>
* <span data-ttu-id="5720b-311">访问`HttpResponse`HTTP 请求完成后的 。</span><span class="sxs-lookup"><span data-stu-id="5720b-311">Accesses the `HttpResponse` after the HTTP request is complete.</span></span>
* <span data-ttu-id="5720b-312">崩溃进程。</span><span class="sxs-lookup"><span data-stu-id="5720b-312">Crashes the process.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

<span data-ttu-id="5720b-313">**执行此操作：** 下面的示例返回 到`Task`框架，因此 HTTP 请求在操作完成之前不会完成。</span><span class="sxs-lookup"><span data-stu-id="5720b-313">**Do this:** The following example returns a `Task` to the framework, so the HTTP request doesn't complete until the action completes.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a><span data-ttu-id="5720b-314">不要在后台线程中捕获 HttpContext</span><span class="sxs-lookup"><span data-stu-id="5720b-314">Do not capture the HttpContext in background threads</span></span>

<span data-ttu-id="5720b-315">**不要这样做：** 下面的示例显示闭包正在从 属性捕获`HttpContext`。 `Controller`</span><span class="sxs-lookup"><span data-stu-id="5720b-315">**Do not do this:** The following example shows a closure is capturing the `HttpContext` from the `Controller` property.</span></span> <span data-ttu-id="5720b-316">这是一个糟糕的做法，因为工作项可以：</span><span class="sxs-lookup"><span data-stu-id="5720b-316">This is a bad practice because the work item could:</span></span>

* <span data-ttu-id="5720b-317">在请求范围之外运行。</span><span class="sxs-lookup"><span data-stu-id="5720b-317">Run outside of the request scope.</span></span>
* <span data-ttu-id="5720b-318">试图读错`HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="5720b-318">Attempt to read the wrong `HttpContext`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

<span data-ttu-id="5720b-319">**执行此操作：** 以下示例：</span><span class="sxs-lookup"><span data-stu-id="5720b-319">**Do this:** The following example:</span></span>

* <span data-ttu-id="5720b-320">复制请求期间后台任务中所需的数据。</span><span class="sxs-lookup"><span data-stu-id="5720b-320">Copies the data required in the background task during the request.</span></span>
* <span data-ttu-id="5720b-321">不引用控制器的任何内容。</span><span class="sxs-lookup"><span data-stu-id="5720b-321">Doesn't reference anything from the controller.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

<span data-ttu-id="5720b-322">后台任务应作为托管服务实现。</span><span class="sxs-lookup"><span data-stu-id="5720b-322">Background tasks should be implemented as hosted services.</span></span> <span data-ttu-id="5720b-323">有关详细信息，请参阅[使用托管服务的后台任务](xref:fundamentals/host/hosted-services)。</span><span class="sxs-lookup"><span data-stu-id="5720b-323">For more information, see [Background tasks with hosted services](xref:fundamentals/host/hosted-services).</span></span>

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a><span data-ttu-id="5720b-324">不要捕获注入后台线程控制器的服务</span><span class="sxs-lookup"><span data-stu-id="5720b-324">Do not capture services injected into the controllers on background threads</span></span>

<span data-ttu-id="5720b-325">**不要这样做：** 下面的示例显示闭包正在从`DbContext``Controller`操作参数捕获 。</span><span class="sxs-lookup"><span data-stu-id="5720b-325">**Do not do this:** The following example shows a closure is capturing the `DbContext` from the `Controller` action parameter.</span></span> <span data-ttu-id="5720b-326">这是一个坏的做法。</span><span class="sxs-lookup"><span data-stu-id="5720b-326">This is a bad practice.</span></span>  <span data-ttu-id="5720b-327">工作项可以运行在请求范围之外。</span><span class="sxs-lookup"><span data-stu-id="5720b-327">The work item could run outside of the request scope.</span></span> <span data-ttu-id="5720b-328">`ContosoDbContext`的范围限定到请求，从而导致 。 `ObjectDisposedException`</span><span class="sxs-lookup"><span data-stu-id="5720b-328">The `ContosoDbContext` is scoped to the request, resulting in an `ObjectDisposedException`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

<span data-ttu-id="5720b-329">**执行此操作：** 以下示例：</span><span class="sxs-lookup"><span data-stu-id="5720b-329">**Do this:** The following example:</span></span>

* <span data-ttu-id="5720b-330">注入<xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>，以便在后台工作项中创建作用域。</span><span class="sxs-lookup"><span data-stu-id="5720b-330">Injects an <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> in order to create a scope in the background work item.</span></span> <span data-ttu-id="5720b-331">`IServiceScopeFactory`是一个单一的。</span><span class="sxs-lookup"><span data-stu-id="5720b-331">`IServiceScopeFactory` is a singleton.</span></span>
* <span data-ttu-id="5720b-332">在后台线程中创建新的依赖项注入作用域。</span><span class="sxs-lookup"><span data-stu-id="5720b-332">Creates a new dependency injection scope in the background thread.</span></span>
* <span data-ttu-id="5720b-333">不引用控制器的任何内容。</span><span class="sxs-lookup"><span data-stu-id="5720b-333">Doesn't reference anything from the controller.</span></span>
* <span data-ttu-id="5720b-334">不从传入请求捕获`ContosoDbContext`。</span><span class="sxs-lookup"><span data-stu-id="5720b-334">Doesn't capture the `ContosoDbContext` from the incoming request.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

<span data-ttu-id="5720b-335">以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="5720b-335">The following highlighted code:</span></span>

* <span data-ttu-id="5720b-336">为后台操作的生存期创建一个作用域，并从中解析服务。</span><span class="sxs-lookup"><span data-stu-id="5720b-336">Creates a scope for the lifetime of the background operation and resolves services from it.</span></span>
* <span data-ttu-id="5720b-337">从`ContosoDbContext`正确的作用域使用。</span><span class="sxs-lookup"><span data-stu-id="5720b-337">Uses `ContosoDbContext` from the correct scope.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a><span data-ttu-id="5720b-338">响应正文启动后，不要修改状态代码或标头</span><span class="sxs-lookup"><span data-stu-id="5720b-338">Do not modify the status code or headers after the response body has started</span></span>

<span data-ttu-id="5720b-339">ASP.NET核心不会缓冲 HTTP 响应正文。</span><span class="sxs-lookup"><span data-stu-id="5720b-339">ASP.NET Core does not buffer the HTTP response body.</span></span> <span data-ttu-id="5720b-340">首次编写响应：</span><span class="sxs-lookup"><span data-stu-id="5720b-340">The first time the response is written:</span></span>

* <span data-ttu-id="5720b-341">标头与正文的块一起发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="5720b-341">The headers are sent along with that chunk of the body to the client.</span></span>
* <span data-ttu-id="5720b-342">不再可以更改响应标头。</span><span class="sxs-lookup"><span data-stu-id="5720b-342">It's no longer possible to change response headers.</span></span>

<span data-ttu-id="5720b-343">**不要这样做：** 以下代码尝试在响应启动后添加响应标头：</span><span class="sxs-lookup"><span data-stu-id="5720b-343">**Do not do this:** The following code tries to add response headers after the response has already started:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

<span data-ttu-id="5720b-344">在前面的代码中，`context.Response.Headers["test"] = "test value";`如果`next()`已写入响应，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="5720b-344">In the preceding code, `context.Response.Headers["test"] = "test value";` will throw an exception if `next()` has written to the response.</span></span>

<span data-ttu-id="5720b-345">**执行此操作：** 以下示例检查 HTTP 响应在修改标头之前是否已启动。</span><span class="sxs-lookup"><span data-stu-id="5720b-345">**Do this:** The following example checks if the HTTP response has started before modifying the headers.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

<span data-ttu-id="5720b-346">**执行此操作：** 以下示例用于`HttpResponse.OnStarting`在响应标头刷新到客户端之前设置标头。</span><span class="sxs-lookup"><span data-stu-id="5720b-346">**Do this:** The following example uses `HttpResponse.OnStarting` to set the headers before the response headers are flushed to the client.</span></span>

<span data-ttu-id="5720b-347">检查响应是否尚未启动，可以注册将在编写响应标头之前调用的回调。</span><span class="sxs-lookup"><span data-stu-id="5720b-347">Checking if the response has not started allows registering a callback that will be invoked just before response headers are written.</span></span> <span data-ttu-id="5720b-348">检查响应是否尚未启动：</span><span class="sxs-lookup"><span data-stu-id="5720b-348">Checking if the response has not started:</span></span>

* <span data-ttu-id="5720b-349">提供及时追加或覆盖标头的能力。</span><span class="sxs-lookup"><span data-stu-id="5720b-349">Provides the ability to append or override headers just in time.</span></span>
* <span data-ttu-id="5720b-350">不需要了解管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="5720b-350">Doesn't require knowledge of the next middleware in the pipeline.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a><span data-ttu-id="5720b-351">如果您已经开始写入响应正文，请不要调用下一个（）</span><span class="sxs-lookup"><span data-stu-id="5720b-351">Do not call next() if you have already started writing to the response body</span></span>

<span data-ttu-id="5720b-352">仅当组件可以处理和操作响应时，才期望调用组件。</span><span class="sxs-lookup"><span data-stu-id="5720b-352">Components only expect to be called if it's possible for them to handle and manipulate the response.</span></span>
