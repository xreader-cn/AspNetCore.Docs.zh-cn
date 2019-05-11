---
title: ASP.NET Core 的性能最佳实践
author: mjrousos
description: 用于提高 ASP.NET Core 应用中的性能和避免常见的性能问题的提示。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 05/10/2019
uid: performance/performance-best-practices
ms.openlocfilehash: 7651dff18f98c60057660c8946c3daa66d272f6a
ms.sourcegitcommit: ffe3ed7921ec6c7c70abaac1d10703ec9a43374c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65536074"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="452f3-103">ASP.NET Core 的性能最佳实践</span><span class="sxs-lookup"><span data-stu-id="452f3-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="452f3-104">通过[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="452f3-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="452f3-105">本主题提供了性能使用 ASP.NET Core 的最佳实践的指导原则。</span><span class="sxs-lookup"><span data-stu-id="452f3-105">This topic provides guidelines for performance best practices with ASP.NET Core.</span></span>

<a name="hot"></a>

<span data-ttu-id="452f3-106">本文档中*热代码路径*定义为代码路径频繁调用的函数和大部分执行时间发生的位置。</span><span class="sxs-lookup"><span data-stu-id="452f3-106">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="452f3-107">热代码路径通常限制应用向外缩放和性能。</span><span class="sxs-lookup"><span data-stu-id="452f3-107">Hot code paths typically limit app scale-out and performance.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="452f3-108">主动缓存</span><span class="sxs-lookup"><span data-stu-id="452f3-108">Cache aggressively</span></span>

<span data-ttu-id="452f3-109">在本文档的多个部分中讨论缓存。</span><span class="sxs-lookup"><span data-stu-id="452f3-109">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="452f3-110">有关详细信息，请参阅 <xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="452f3-110">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="452f3-111">避免阻止调用</span><span class="sxs-lookup"><span data-stu-id="452f3-111">Avoid blocking calls</span></span>

<span data-ttu-id="452f3-112">ASP.NET Core 应用程序应设计为同时处理多个请求。</span><span class="sxs-lookup"><span data-stu-id="452f3-112">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="452f3-113">异步 Api 允许线程通过不在阻塞调用在等待处理数千个并发请求的小型池。</span><span class="sxs-lookup"><span data-stu-id="452f3-113">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="452f3-114">而不是等待长时间运行的同步任务，才能完成，线程可以处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="452f3-114">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="452f3-115">ASP.NET Core 应用中的常见性能问题正在阻止可能是异步调用。</span><span class="sxs-lookup"><span data-stu-id="452f3-115">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="452f3-116">很多同步阻塞调用会导致[线程池资源不足](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)和下降响应时间。</span><span class="sxs-lookup"><span data-stu-id="452f3-116">Many synchronous blocking calls lead to [Thread Pool starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) and degraded response times.</span></span>

<span data-ttu-id="452f3-117">**不这样做**:</span><span class="sxs-lookup"><span data-stu-id="452f3-117">**Do not**:</span></span>

* <span data-ttu-id="452f3-118">通过调用块异步执行[Task.Wait](/dotnet/api/system.threading.tasks.task.wait)或[Task.Result](/dotnet/api/system.threading.tasks.task-1.result)。</span><span class="sxs-lookup"><span data-stu-id="452f3-118">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="452f3-119">获取在常见的代码路径中的锁。</span><span class="sxs-lookup"><span data-stu-id="452f3-119">Acquire locks in common code paths.</span></span> <span data-ttu-id="452f3-120">ASP.NET Core 应用是大多数的高性能设计为可并行运行代码时。</span><span class="sxs-lookup"><span data-stu-id="452f3-120">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>

<span data-ttu-id="452f3-121">**执行**:</span><span class="sxs-lookup"><span data-stu-id="452f3-121">**Do**:</span></span>

* <span data-ttu-id="452f3-122">请[热代码路径](#hot)异步。</span><span class="sxs-lookup"><span data-stu-id="452f3-122">Make [hot code paths](#hot) asynchronous.</span></span>
* <span data-ttu-id="452f3-123">以异步方式调用数据访问和长时间运行的操作的 Api。</span><span class="sxs-lookup"><span data-stu-id="452f3-123">Call data access and long-running operations APIs asynchronously.</span></span>
* <span data-ttu-id="452f3-124">将控制器/Razor 页面操作异步。</span><span class="sxs-lookup"><span data-stu-id="452f3-124">Make controller/Razor Page actions asynchronous.</span></span> <span data-ttu-id="452f3-125">整个调用堆栈是为了受益于异步[异步/等待](/dotnet/csharp/programming-guide/concepts/async/)模式。</span><span class="sxs-lookup"><span data-stu-id="452f3-125">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="452f3-126">探查器，如[PerfView](https://github.com/Microsoft/perfview)，可用于查找经常会添加到线程[线程池](/windows/desktop/procthread/thread-pools)。</span><span class="sxs-lookup"><span data-stu-id="452f3-126">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="452f3-127">`Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`事件指示添加到线程池线程。</span><span class="sxs-lookup"><span data-stu-id="452f3-127">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc  -->

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="452f3-128">最大程度减少大型对象分配</span><span class="sxs-lookup"><span data-stu-id="452f3-128">Minimize large object allocations</span></span>

<!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core -->
<span data-ttu-id="452f3-129">[.NET Core 垃圾回收器](/dotnet/standard/garbage-collection/)管理的自动在 ASP.NET Core 应用中的内存分配和释放。</span><span class="sxs-lookup"><span data-stu-id="452f3-129">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="452f3-130">自动垃圾回收通常意味着开发人员无需担心如何或何时释放内存。</span><span class="sxs-lookup"><span data-stu-id="452f3-130">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="452f3-131">但是，清理未引用的对象会占用 CPU 时间，因此开发人员应尽量减少在分配对象[热代码路径](#hot)。</span><span class="sxs-lookup"><span data-stu-id="452f3-131">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#hot).</span></span> <span data-ttu-id="452f3-132">垃圾回收，尤其是昂贵大型对象 （> 85 K 字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="452f3-132">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="452f3-133">大型对象存储在[大型对象堆](/dotnet/standard/garbage-collection/large-object-heap)和要求的完整 （第 2 代） 垃圾回收以进行清除。</span><span class="sxs-lookup"><span data-stu-id="452f3-133">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="452f3-134">与 0 代和第 1 代回收，不同的第 2 代收集需要应用程序执行临时的挂起。</span><span class="sxs-lookup"><span data-stu-id="452f3-134">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="452f3-135">频繁分配和解除分配的大型对象可能导致不一致的性能。</span><span class="sxs-lookup"><span data-stu-id="452f3-135">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="452f3-136">建议：</span><span class="sxs-lookup"><span data-stu-id="452f3-136">Recommendations:</span></span>

* <span data-ttu-id="452f3-137">**执行**考虑缓存经常使用的大型对象。</span><span class="sxs-lookup"><span data-stu-id="452f3-137">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="452f3-138">缓存大型对象防止昂贵的分配。</span><span class="sxs-lookup"><span data-stu-id="452f3-138">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="452f3-139">**不要**池使用的缓冲区[ `ArrayPool<T>` ](/dotnet/api/system.buffers.arraypool-1)来存储大数组。</span><span class="sxs-lookup"><span data-stu-id="452f3-139">**Do** pool buffers by using an [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="452f3-140">**不这样做**上分配了很多，生存期较短的大型对象[热代码路径](#hot)。</span><span class="sxs-lookup"><span data-stu-id="452f3-140">**Do not** allocate many, short-lived large objects on [hot code paths](#hot).</span></span>

<span data-ttu-id="452f3-141">可以通过查看垃圾回收 (GC) 中的统计信息来诊断内存问题，如所述， [PerfView](https://github.com/Microsoft/perfview)并检查：</span><span class="sxs-lookup"><span data-stu-id="452f3-141">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="452f3-142">垃圾回收暂停时间。</span><span class="sxs-lookup"><span data-stu-id="452f3-142">Garbage collection pause time.</span></span>
* <span data-ttu-id="452f3-143">垃圾回收中所用的处理器时间百分比。</span><span class="sxs-lookup"><span data-stu-id="452f3-143">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="452f3-144">多少垃圾回收是 0、 1 和 2 代。</span><span class="sxs-lookup"><span data-stu-id="452f3-144">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="452f3-145">有关详细信息，请参阅[垃圾回收和性能](/dotnet/standard/garbage-collection/performance)。</span><span class="sxs-lookup"><span data-stu-id="452f3-145">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access"></a><span data-ttu-id="452f3-146">优化数据访问</span><span class="sxs-lookup"><span data-stu-id="452f3-146">Optimize Data Access</span></span>

<span data-ttu-id="452f3-147">与数据存储区和其他远程服务之间的交互通常是 ASP.NET Core 应用的速度最慢部分。</span><span class="sxs-lookup"><span data-stu-id="452f3-147">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="452f3-148">读取和写入数据有效地实现良好性能的关键。</span><span class="sxs-lookup"><span data-stu-id="452f3-148">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="452f3-149">建议：</span><span class="sxs-lookup"><span data-stu-id="452f3-149">Recommendations:</span></span>

* <span data-ttu-id="452f3-150">**执行**以异步方式调用 Api 的所有数据访问。</span><span class="sxs-lookup"><span data-stu-id="452f3-150">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="452f3-151">**不这样做**检索不必要的更多数据。</span><span class="sxs-lookup"><span data-stu-id="452f3-151">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="452f3-152">编写查询以返回不仅仅是当前 HTTP 请求所需的数据。</span><span class="sxs-lookup"><span data-stu-id="452f3-152">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="452f3-153">**执行**考虑缓存经常访问的数据检索从数据库或远程服务，如果略有过时的数据是可接受。</span><span class="sxs-lookup"><span data-stu-id="452f3-153">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="452f3-154">根据方案，使用[MemoryCache](xref:performance/caching/memory)或[DistributedCache](xref:performance/caching/distributed)。</span><span class="sxs-lookup"><span data-stu-id="452f3-154">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="452f3-155">有关详细信息，请参阅 <xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="452f3-155">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="452f3-156">**执行**最大程度减少网络往返。</span><span class="sxs-lookup"><span data-stu-id="452f3-156">**Do** minimize network round trips.</span></span> <span data-ttu-id="452f3-157">目标是检索一次调用而不是多个调用中所需的数据。</span><span class="sxs-lookup"><span data-stu-id="452f3-157">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="452f3-158">**不要**使用[无跟踪查询](/ef/core/querying/tracking#no-tracking-queries)中 Entity Framework Core 用于只读目的访问数据时。</span><span class="sxs-lookup"><span data-stu-id="452f3-158">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="452f3-159">EF Core 可以更有效地返回非跟踪查询的结果。</span><span class="sxs-lookup"><span data-stu-id="452f3-159">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="452f3-160">**不要**筛选器和聚合的 LINQ 查询 (使用`.Where`， `.Select`，或`.Sum`语句，例如)，以便筛选由数据库执行。</span><span class="sxs-lookup"><span data-stu-id="452f3-160">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="452f3-161">**执行**考虑 EF Core 解析客户端可能会导致效率低下的查询执行一些查询运算符。</span><span class="sxs-lookup"><span data-stu-id="452f3-161">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="452f3-162">有关详细信息，请参阅[客户端评估性能问题](/ef/core/querying/client-eval#client-evaluation-performance-issues)。</span><span class="sxs-lookup"><span data-stu-id="452f3-162">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="452f3-163">**不这样做**使用投影查询集合，这可能会导致在执行"N + 1"上的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="452f3-163">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="452f3-164">有关详细信息，请参阅[的相关子查询的优化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)。</span><span class="sxs-lookup"><span data-stu-id="452f3-164">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="452f3-165">请参阅[EF 高性能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)高规模的应用中可提高性能的方法：</span><span class="sxs-lookup"><span data-stu-id="452f3-165">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="452f3-166">DbContext 池</span><span class="sxs-lookup"><span data-stu-id="452f3-166">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="452f3-167">显式编译的查询</span><span class="sxs-lookup"><span data-stu-id="452f3-167">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="452f3-168">我们建议测量之前提交基本代码的前面高效的方法的影响。</span><span class="sxs-lookup"><span data-stu-id="452f3-168">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="452f3-169">已编译的查询的其他复杂问题可能不产生的性能改进。</span><span class="sxs-lookup"><span data-stu-id="452f3-169">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="452f3-170">可以通过查看时检测到问题的查询与访问数据所花[Application Insights](/azure/application-insights/app-insights-overview)或使用分析工具。</span><span class="sxs-lookup"><span data-stu-id="452f3-170">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="452f3-171">大多数数据库还提供统计信息与有关常执行的查询。</span><span class="sxs-lookup"><span data-stu-id="452f3-171">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="452f3-172">与 HttpClientFactory 池 HTTP 连接</span><span class="sxs-lookup"><span data-stu-id="452f3-172">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="452f3-173">尽管[HttpClient](/dotnet/api/system.net.http.httpclient)实现`IDisposable`接口，目的是以供重复使用。</span><span class="sxs-lookup"><span data-stu-id="452f3-173">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="452f3-174">关闭`HttpClient`实例将在中打开套接字`TIME_WAIT`状态的短的时间。</span><span class="sxs-lookup"><span data-stu-id="452f3-174">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="452f3-175">如果创建和释放的代码路径`HttpClient`频繁使用对象，该应用程序可能会出现耗尽可用的套接字。</span><span class="sxs-lookup"><span data-stu-id="452f3-175">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="452f3-176">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)中引入了 ASP.NET Core 2.1 作为一种解决方案对此问题。</span><span class="sxs-lookup"><span data-stu-id="452f3-176">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="452f3-177">它处理共用的 HTTP 连接来优化性能和可靠性。</span><span class="sxs-lookup"><span data-stu-id="452f3-177">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="452f3-178">建议：</span><span class="sxs-lookup"><span data-stu-id="452f3-178">Recommendations:</span></span>

* <span data-ttu-id="452f3-179">**不这样做**创建和释放`HttpClient`直接实例。</span><span class="sxs-lookup"><span data-stu-id="452f3-179">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="452f3-180">**不要**使用[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)检索`HttpClient`实例。</span><span class="sxs-lookup"><span data-stu-id="452f3-180">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="452f3-181">有关详细信息，请参阅[来实现可复原的 HTTP 请求的使用 HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)。</span><span class="sxs-lookup"><span data-stu-id="452f3-181">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="452f3-182">保持快速的常见代码路径</span><span class="sxs-lookup"><span data-stu-id="452f3-182">Keep common code paths fast</span></span>

<span data-ttu-id="452f3-183">您希望所有代码以进行快速、 经常调用的代码路径都是最重要优化：</span><span class="sxs-lookup"><span data-stu-id="452f3-183">You want all of your code to be fast, frequently called code paths are the most critical to optimize:</span></span>

* <span data-ttu-id="452f3-184">应用程序的请求处理管道中的中间件组件，尤其是中间件运行尽早在管道中。</span><span class="sxs-lookup"><span data-stu-id="452f3-184">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="452f3-185">这些组件对性能产生很大的影响。</span><span class="sxs-lookup"><span data-stu-id="452f3-185">These components have a large impact on performance.</span></span>
* <span data-ttu-id="452f3-186">为每个请求或多个时间每个请求执行的代码。</span><span class="sxs-lookup"><span data-stu-id="452f3-186">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="452f3-187">例如，自定义日志记录、 授权处理程序或暂时服务的初始化。</span><span class="sxs-lookup"><span data-stu-id="452f3-187">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="452f3-188">建议：</span><span class="sxs-lookup"><span data-stu-id="452f3-188">Recommendations:</span></span>

* <span data-ttu-id="452f3-189">**不这样做**与长时间运行的任务中使用自定义中间件组件。</span><span class="sxs-lookup"><span data-stu-id="452f3-189">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="452f3-190">**不要**使用性能分析工具，如[Visual Studio 诊断工具](/visualstudio/profiling/profiling-feature-tour)或[PerfView](https://github.com/Microsoft/perfview))，以便标识[热代码路径](#hot)。</span><span class="sxs-lookup"><span data-stu-id="452f3-190">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#hot).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="452f3-191">完成长时间运行的任务之外的 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="452f3-191">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="452f3-192">到 ASP.NET Core 应用的请求最多可以处理由控制器或调用必要的服务，并返回 HTTP 响应的页面模型。</span><span class="sxs-lookup"><span data-stu-id="452f3-192">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="452f3-193">对于某些涉及长时间运行的任务的请求，它是更好的做法使整个请求-响应过程异步。</span><span class="sxs-lookup"><span data-stu-id="452f3-193">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="452f3-194">建议：</span><span class="sxs-lookup"><span data-stu-id="452f3-194">Recommendations:</span></span>

* <span data-ttu-id="452f3-195">**不这样做**等待长时间运行的任务以完成作为普通 HTTP 请求处理的一部分。</span><span class="sxs-lookup"><span data-stu-id="452f3-195">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="452f3-196">**不要**处理与长时间运行请求，请考虑[后台服务](xref:fundamentals/host/hosted-services)或与进程外[Azure Function](/azure/azure-functions/)。</span><span class="sxs-lookup"><span data-stu-id="452f3-196">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="452f3-197">完成的工作进程外是特别适用于 CPU 密集型任务。</span><span class="sxs-lookup"><span data-stu-id="452f3-197">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="452f3-198">**不要**使用实时通信选项，如[SignalR](xref:signalr/introduction)、 以异步方式与客户端通信。</span><span class="sxs-lookup"><span data-stu-id="452f3-198">**Do** use real-time communication options, such as [SignalR](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="452f3-199">缩小客户端资产</span><span class="sxs-lookup"><span data-stu-id="452f3-199">Minify client assets</span></span>

<span data-ttu-id="452f3-200">许多 JavaScript、 CSS 或图像文件，通常服务于复杂的前端使用 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="452f3-200">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="452f3-201">可以通过提高的初始加载请求的性能：</span><span class="sxs-lookup"><span data-stu-id="452f3-201">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="452f3-202">该绑定，将多个文件合并成一个。</span><span class="sxs-lookup"><span data-stu-id="452f3-202">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="452f3-203">这会缩小，减少通过删除空格和提出的意见文件大小。</span><span class="sxs-lookup"><span data-stu-id="452f3-203">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="452f3-204">建议：</span><span class="sxs-lookup"><span data-stu-id="452f3-204">Recommendations:</span></span>

* <span data-ttu-id="452f3-205">**不要**使用 ASP.NET Core[内置支持](xref:client-side/bundling-and-minification)进行捆绑和缩小客户端资产。</span><span class="sxs-lookup"><span data-stu-id="452f3-205">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="452f3-206">**不要**考虑其他第三方工具，例如[Webpack](https://webpack.js.org/)，复杂的客户端管理资产。</span><span class="sxs-lookup"><span data-stu-id="452f3-206">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="452f3-207">压缩响应</span><span class="sxs-lookup"><span data-stu-id="452f3-207">Compress responses</span></span>

 <span data-ttu-id="452f3-208">通常减少响应的大小通常显著增加的应用的响应能力。</span><span class="sxs-lookup"><span data-stu-id="452f3-208">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="452f3-209">若要减少有效负载大小的一种方法是压缩应用程序的响应。</span><span class="sxs-lookup"><span data-stu-id="452f3-209">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="452f3-210">有关详细信息，请参阅[响应压缩](xref:performance/response-compression)。</span><span class="sxs-lookup"><span data-stu-id="452f3-210">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="452f3-211">使用最新的 ASP.NET Core 版本</span><span class="sxs-lookup"><span data-stu-id="452f3-211">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="452f3-212">每个新版本的 ASP.NET Core 包含性能改进。</span><span class="sxs-lookup"><span data-stu-id="452f3-212">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="452f3-213">.NET Core 和 ASP.NET Core 中的优化意味着较新版本通常优于较旧版本。</span><span class="sxs-lookup"><span data-stu-id="452f3-213">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="452f3-214">例如，.NET Core 2.1 添加了支持的已编译的正则表达式和出来[ `Span<T>` ](https://msdn.microsoft.com/magazine/mt814808.aspx)。</span><span class="sxs-lookup"><span data-stu-id="452f3-214">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx).</span></span> <span data-ttu-id="452f3-215">ASP.NET Core 2.2 添加支持 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="452f3-215">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="452f3-216">如果性能是优先级，请考虑升级到 ASP.NET Core 的当前版本。</span><span class="sxs-lookup"><span data-stu-id="452f3-216">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

<!-- TODO review link and taking advantage of new [performance features](#TBD)
Maybe skip this TBD link as each version will have perf improvements -->

## <a name="minimize-exceptions"></a><span data-ttu-id="452f3-217">最大程度减少异常</span><span class="sxs-lookup"><span data-stu-id="452f3-217">Minimize exceptions</span></span>

<span data-ttu-id="452f3-218">异常应该很少见。</span><span class="sxs-lookup"><span data-stu-id="452f3-218">Exceptions should be rare.</span></span> <span data-ttu-id="452f3-219">引发和捕获异常速度较慢相对于其他代码流模式。</span><span class="sxs-lookup"><span data-stu-id="452f3-219">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="452f3-220">正因为如此，不应使用异常来控制正常程序流。</span><span class="sxs-lookup"><span data-stu-id="452f3-220">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="452f3-221">建议：</span><span class="sxs-lookup"><span data-stu-id="452f3-221">Recommendations:</span></span>

* <span data-ttu-id="452f3-222">**不这样做**引发或捕捉异常作为一种正常程序流中，尤其是在使用[热代码路径](#hot)。</span><span class="sxs-lookup"><span data-stu-id="452f3-222">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#hot).</span></span>
* <span data-ttu-id="452f3-223">**执行**逻辑包括在应用程序以检测和处理会导致异常的条件。</span><span class="sxs-lookup"><span data-stu-id="452f3-223">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="452f3-224">**执行**引发或捕获异常的异常或意外条件。</span><span class="sxs-lookup"><span data-stu-id="452f3-224">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="452f3-225">应用程序诊断工具，如 Application Insights 可以帮助识别可能会影响性能的应用中常见的异常。</span><span class="sxs-lookup"><span data-stu-id="452f3-225">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>
