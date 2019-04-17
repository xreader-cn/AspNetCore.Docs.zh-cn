---
title: ASP.NET Core 的性能最佳实践
author: mjrousos
description: 用于提高 ASP.NET Core 应用中的性能和避免常见的性能问题的提示。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/13/2019
uid: performance/performance-best-practices
ms.openlocfilehash: 095db38cf3102f6e18930efdbbaeeb90dffad8af
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614443"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Core 的性能最佳实践

通过[Mike Rousos](https://github.com/mjrousos)

本主题提供了性能使用 ASP.NET Core 的最佳实践的指导原则。

<a name="hot"></a>

本文档中*热代码路径*定义为代码路径频繁调用的函数和大部分执行时间发生的位置。 热代码路径通常限制应用向外缩放和性能。

## <a name="cache-aggressively"></a>主动缓存

在本文档的多个部分中讨论缓存。 有关详细信息，请参阅 <xref:performance/caching/response>。

## <a name="avoid-blocking-calls"></a>避免阻止调用

ASP.NET Core 应用程序应设计为同时处理多个请求。 异步 Api 允许线程通过不在阻塞调用在等待处理数千个并发请求的小型池。 而不是等待长时间运行的同步任务，才能完成，线程可以处理其他请求。

ASP.NET Core 应用中的常见性能问题正在阻止可能是异步调用。 很多同步阻塞调用会导致[线程池资源不足](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)和下降响应时间。

**不这样做**:

* 通过调用块异步执行[Task.Wait](/dotnet/api/system.threading.tasks.task.wait)或[Task.Result](/dotnet/api/system.threading.tasks.task-1.result)。
* 获取在常见的代码路径中的锁。 ASP.NET Core 应用是大多数的高性能设计为可并行运行代码时。

**执行**:

* 请[热代码路径](#hot)异步。
* 以异步方式调用数据访问和长时间运行的操作的 Api。
* 将控制器/Razor 页面操作异步。 整个调用堆栈是为了受益于异步[异步/等待](/dotnet/csharp/programming-guide/concepts/async/)模式。

探查器，如[PerfView](https://github.com/Microsoft/perfview)，可用于查找经常会添加到线程[线程池](/windows/desktop/procthread/thread-pool)。 `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`事件指示添加到线程池线程。 <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc  -->

## <a name="minimize-large-object-allocations"></a>最大程度减少大型对象分配

<!-- TODO review Bill - replaced original .NET language below with .NET Core since this targets .NET Core -->
[.NET Core 垃圾回收器](/dotnet/standard/garbage-collection/)管理的自动在 ASP.NET Core 应用中的内存分配和释放。 自动垃圾回收通常意味着开发人员无需担心如何或何时释放内存。 但是，清理未引用的对象会占用 CPU 时间，因此开发人员应尽量减少在分配对象[热代码路径](#hot)。 垃圾回收，尤其是昂贵大型对象 （> 85 K 字节为单位）。 大型对象存储在[大型对象堆](/dotnet/standard/garbage-collection/large-object-heap)和要求的完整 （第 2 代） 垃圾回收以进行清除。 与 0 代和第 1 代回收，不同的第 2 代收集需要应用程序执行临时的挂起。 频繁分配和解除分配的大型对象可能导致不一致的性能。

建议：

* **执行**考虑缓存经常使用的大型对象。 缓存大型对象防止昂贵的分配。
* **不要**池使用的缓冲区[ `ArrayPool<T>` ](/dotnet/api/system.buffers.arraypool-1)来存储大数组。
* **不这样做**上分配了很多，生存期较短的大型对象[热代码路径](#hot)。

可以通过查看垃圾回收 (GC) 中的统计信息来诊断内存问题，如所述， [PerfView](https://github.com/Microsoft/perfview)并检查：

* 垃圾回收暂停时间。
* 垃圾回收中所用的处理器时间百分比。
* 多少垃圾回收是 0、 1 和 2 代。

有关详细信息，请参阅[垃圾回收和性能](/dotnet/standard/garbage-collection/performance)。

## <a name="optimize-data-access"></a>优化数据访问

与数据存储区和其他远程服务之间的交互通常是 ASP.NET Core 应用的速度最慢部分。 读取和写入数据有效地实现良好性能的关键。

建议：

* **执行**以异步方式调用 Api 的所有数据访问。
* **不这样做**检索不必要的更多数据。 编写查询以返回不仅仅是当前 HTTP 请求所需的数据。
* **执行**考虑缓存经常访问的数据检索从数据库或远程服务，如果略有过时的数据是可接受。 根据方案，使用[MemoryCache](xref:performance/caching/memory)或[DistributedCache](xref:performance/caching/distributed)。 有关详细信息，请参阅 <xref:performance/caching/response>。
* **执行**最大程度减少网络往返。 目标是检索一次调用而不是多个调用中所需的数据。
* **不要**使用[无跟踪查询](/ef/core/querying/tracking#no-tracking-queries)中 Entity Framework Core 用于只读目的访问数据时。 EF Core 可以更有效地返回非跟踪查询的结果。
* **不要**筛选器和聚合的 LINQ 查询 (使用`.Where`， `.Select`，或`.Sum`语句，例如)，以便筛选由数据库执行。
* **执行**考虑 EF Core 解析客户端可能会导致效率低下的查询执行一些查询运算符。 有关详细信息，请参阅[客户端评估性能问题](/ef/core/querying/client-eval#client-evaluation-performance-issues)。
* **不这样做**使用投影查询集合，这可能会导致在执行"N + 1"上的 SQL 查询。 有关详细信息，请参阅[的相关子查询的优化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)。

请参阅[EF 高性能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)高规模的应用中可提高性能的方法：

* [DbContext 池](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [显式编译的查询](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

我们建议测量之前提交基本代码的前面高效的方法的影响。 已编译的查询的其他复杂问题可能不产生的性能改进。

可以通过查看时检测到问题的查询与访问数据所花[Application Insights](/azure/application-insights/app-insights-overview)或使用分析工具。 大多数数据库还提供统计信息与有关常执行的查询。

## <a name="pool-http-connections-with-httpclientfactory"></a>与 HttpClientFactory 池 HTTP 连接

尽管[HttpClient](/dotnet/api/system.net.http.httpclient)实现`IDisposable`接口，目的是以供重复使用。 关闭`HttpClient`实例将在中打开套接字`TIME_WAIT`状态的短的时间。 如果创建和释放的代码路径`HttpClient`频繁使用对象，该应用程序可能会出现耗尽可用的套接字。 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)中引入了 ASP.NET Core 2.1 作为一种解决方案对此问题。 它处理共用的 HTTP 连接来优化性能和可靠性。

建议：

* **不这样做**创建和释放`HttpClient`直接实例。
* **不要**使用[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)检索`HttpClient`实例。 有关详细信息，请参阅[来实现可复原的 HTTP 请求的使用 HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)。

## <a name="keep-common-code-paths-fast"></a>保持快速的常见代码路径

您希望所有代码以进行快速、 经常调用的代码路径都是最重要优化：

* 应用程序的请求处理管道中的中间件组件，尤其是中间件运行尽早在管道中。 这些组件对性能产生很大的影响。
* 为每个请求或多个时间每个请求执行的代码。 例如，自定义日志记录、 授权处理程序或暂时服务的初始化。

建议：

* **不这样做**与长时间运行的任务中使用自定义中间件组件。
* **不要**使用性能分析工具，如[Visual Studio 诊断工具](/visualstudio/profiling/profiling-feature-tour)或[PerfView](https://github.com/Microsoft/perfview))，以便标识[热代码路径](#hot)。

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>完成长时间运行的任务之外的 HTTP 请求

到 ASP.NET Core 应用的请求最多可以处理由控制器或调用必要的服务，并返回 HTTP 响应的页面模型。 对于某些涉及长时间运行的任务的请求，它是更好的做法使整个请求-响应过程异步。

建议：

* **不这样做**等待长时间运行的任务以完成作为普通 HTTP 请求处理的一部分。
* **不要**处理与长时间运行请求，请考虑[后台服务](xref:fundamentals/host/hosted-services)或与进程外[Azure Function](/azure/azure-functions/)。 完成的工作进程外是特别适用于 CPU 密集型任务。
* **不要**使用实时通信选项，如[SignalR](xref:signalr/introduction)、 以异步方式与客户端通信。

## <a name="minify-client-assets"></a>缩小客户端资产

许多 JavaScript、 CSS 或图像文件，通常服务于复杂的前端使用 ASP.NET Core 应用。 可以通过提高的初始加载请求的性能：

* 该绑定，将多个文件合并成一个。
* 这会缩小，减少通过删除空格和提出的意见文件大小。

建议：

* **不要**使用 ASP.NET Core[内置支持](xref:client-side/bundling-and-minification)进行捆绑和缩小客户端资产。
* **不要**考虑其他第三方工具，例如[Gulp](xref:client-side/using-gulp)或[Webpack](https://webpack.js.org/)复杂的客户端管理资产。

## <a name="compress-responses"></a>压缩响应

 通常减少响应的大小通常显著增加的应用的响应能力。 若要减少有效负载大小的一种方法是压缩应用程序的响应。 有关详细信息，请参阅[响应压缩](xref:performance/response-compression)。

## <a name="use-the-latest-aspnet-core-release"></a>使用最新的 ASP.NET Core 版本

每个新版本的 ASP.NET Core 包含性能改进。 .NET Core 和 ASP.NET Core 中的优化意味着较新版本通常优于较旧版本。 例如，.NET Core 2.1 添加了支持的已编译的正则表达式和出来[ `Span<T>` ](https://msdn.microsoft.com/magazine/mt814808.aspx)。 ASP.NET Core 2.2 添加支持 HTTP/2。 如果性能是优先级，请考虑升级到 ASP.NET Core 的当前版本。

<!-- TODO review link and taking advantage of new [performance features](#TBD)
Maybe skip this TBD link as each version will have perf improvements -->

## <a name="minimize-exceptions"></a>最大程度减少异常

异常应该很少见。 引发和捕获异常速度较慢相对于其他代码流模式。 正因为如此，不应使用异常来控制正常程序流。

建议：

* **不这样做**引发或捕捉异常作为一种正常程序流中，尤其是在使用[热代码路径](#hot)。
* **执行**逻辑包括在应用程序以检测和处理会导致异常的条件。
* **执行**引发或捕获异常的异常或意外条件。

应用程序诊断工具，如 Application Insights 可以帮助识别可能会影响性能的应用中常见的异常。
