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
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET核心性能最佳实践

作者：[Mike Rousos](https://github.com/mjrousos)

本文提供了ASP.NET核心的性能最佳实践指南。

## <a name="cache-aggressively"></a>主动缓存

本文档的几个部分讨论了缓存。 有关详细信息，请参阅 <xref:performance/caching/response>。

## <a name="understand-hot-code-paths"></a>了解热代码路径

在本文中，*热代码路径*定义为经常调用的代码路径以及大部分执行时间发生的位置。 热代码路径通常限制应用横向扩展和性能，本文档的几个部分对此进行讨论。

## <a name="avoid-blocking-calls"></a>避免阻塞呼叫

ASP.NET核心应用应设计为同时处理许多请求。 异步 API 允许小线程池通过不等待阻塞调用来处理数千个并发请求。 线程可以处理另一个请求，而不是等待长时间运行的同步任务来完成。

ASP.NET Core 应用中常见的性能问题是阻止可能是异步的调用。 许多同步阻塞调用会导致[线程池不足](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)和响应时间降低。

**不要**：

* 通过调用[Task.Wait](/dotnet/api/system.threading.tasks.task.wait)或[Task.结果](/dotnet/api/system.threading.tasks.task-1.result)来阻止异步执行。
* 获取公共代码路径中的锁。 ASP.NET核心应用在构建为并行运行代码时性能最出色。
* 调用[任务.运行](/dotnet/api/system.threading.tasks.task.run)并立即等待它。 ASP.NET Core 已经在正常的线程池线程上运行应用代码，因此调用 Task.Run 只会导致不必要的线程池计划。 即使计划的代码会阻止线程，Task.Run 也不会阻止这一点。

**做**：

* 使[热代码路径](#understand-hot-code-paths)异步。
* 如果异步 API 可用，则异步 API 会异步调用数据访问、I/O 和长期运行的操作 API。 **不要**使用[Task.Run](/dotnet/api/system.threading.tasks.task.run)使同步 API 异步。
* 使控制器/Razor 页面操作异步。 整个调用堆栈是异步的，以便从[异步/等待](/dotnet/csharp/programming-guide/concepts/async/)模式中获益。

探查器（如[PerfView）](https://github.com/Microsoft/perfview)可用于查找经常添加到[线程池的线程](/windows/desktop/procthread/thread-pools)。 该`Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`事件指示添加到线程池的线程。 <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>最小化大型对象分配

[.NET 核心垃圾回收器](/dotnet/standard/garbage-collection/)在 ASP.NET 核心应用中自动管理内存的分配和释放。 自动垃圾回收通常意味着开发人员无需担心如何或何时释放内存。 但是，清理未引用的对象需要 CPU 时间，因此开发人员应尽量减少[在热代码路径](#understand-hot-code-paths)中分配对象。 垃圾回收对于大型对象（> 85 K 字节）特别昂贵。 大型对象存储在[大型对象堆](/dotnet/standard/garbage-collection/large-object-heap)上，需要完整（第 2 代）垃圾回收来清理。 与第 0 代和第 1 代集合不同，第 2 代集合需要暂时暂停应用执行。 频繁分配和取消分配大型对象可能会导致性能不一致。

建议：

* **请考虑**缓存经常使用的大型对象。 缓存大型对象可防止昂贵的分配。
* 使用[ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1)存储大型数组来**执行**池缓冲区。
* **不要**在[热代码路径](#understand-hot-code-paths)上分配许多短寿命的大对象。

可以通过查看[PerfView](https://github.com/Microsoft/perfview)中的垃圾回收 （GC） 统计信息并检查内存问题（如上述问题）来诊断：

* 垃圾回收暂停时间。
* 在垃圾回收中花费的处理器时间的百分比。
* 有多少垃圾回收是生成 0、1 和 2。

有关详细信息，请参阅[垃圾回收和性能](/dotnet/standard/garbage-collection/performance)。

## <a name="optimize-data-access-and-io"></a>优化数据访问和 I/O

与数据存储和其他远程服务的交互通常是ASP.NET核心应用中最慢的部分。 高效读取和写入数据对于良好的性能至关重要。

建议：

* **请**异步调用所有数据访问 API。
* **不要**检索超过所需数据的数据。 编写查询以仅返回当前 HTTP 请求所需的数据。
* **如果**可以接受稍微过时的数据，请考虑缓存从数据库或远程服务检索的频繁访问的数据。 根据方案，使用[内存缓存](xref:performance/caching/memory)或[分布式缓存](xref:performance/caching/distributed)。 有关详细信息，请参阅 <xref:performance/caching/response>。
* **尽量减少**网络往返。 目标是在单个调用中检索所需的数据，而不是多个调用。
* 出于只读目的访问数据时，**请**使用实体框架核心中的[不跟踪查询](/ef/core/querying/tracking#no-tracking-queries)。 EF Core 可以更有效地返回无跟踪查询的结果。
* **执行**筛选和聚合 LINQ 查询（`.Where`例如`.Select`，使用`.Sum`或 语句），以便由数据库执行筛选。
* **一应考虑**EF Core 解析客户端上的一些查询运算符，这可能导致查询执行效率低下。 有关详细信息，请参阅[客户端评估性能问题](/ef/core/querying/client-eval#client-evaluation-performance-issues)。
* **不要**对集合使用投影查询，这可能导致执行"N + 1"SQL 查询。 有关详细信息，请参阅[优化相关子查询](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)。

有关可能提高大规模应用中性能的方法，请参阅[EF 高性能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)：

* [DbContext 池](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [显式编译的查询](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

我们建议在提交代码库之前测量上述高性能方法的影响。 编译查询的额外复杂性可能无法证明性能改进的合理性。

可以通过查看使用[应用程序见解](/azure/application-insights/app-insights-overview)或分析工具访问数据所花费的时间来检测查询问题。 大多数数据库还提供有关频繁执行的查询的统计信息。

## <a name="pool-http-connections-with-httpclientfactory"></a>使用 HttpClientFactory 池 HTTP 连接

尽管[HttpClient](/dotnet/api/system.net.http.httpclient) `IDisposable`实现了该接口，但它旨在重用。 关闭`HttpClient`的实例使套接字处于`TIME_WAIT`状态短时间打开。 如果经常使用创建和释放`HttpClient`对象的代码路径，则应用可能会耗尽可用的套接字。 [httpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)在 ASP.NET 核心 2.1 中引入，作为此问题的解决方案。 它处理池 HTTP 连接以优化性能和可靠性。

建议：

* **不要**直接创建和处置`HttpClient`实例。
* **请使用** [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)检索`HttpClient`实例。 有关详细信息，请参阅[使用 HttpClientFactory 实现可复原的 HTTP 请求](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)。

## <a name="keep-common-code-paths-fast"></a>保持常见代码路径快速

您希望所有代码都快速。 经常调用的代码路径是优化的最重要路径。 其中包括:

* 应用请求处理管道中的中间件组件，尤其是中间件在管道中早期运行。 这些组件对性能有很大影响。
* 为每个请求执行的代码或每个请求多次执行的代码。 例如，自定义日志记录、授权处理程序或暂态服务的初始化。

建议：

* **请勿**将自定义中间件组件用于长时间运行的任务。
* **请使用**性能分析工具（如[可视化工作室诊断工具](/visualstudio/profiling/profiling-feature-tour)或[PerfView）](https://github.com/Microsoft/perfview)来标识[热代码路径](#understand-hot-code-paths)。

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>在 HTTP 请求之外完成长时间运行的任务

对ASP.NET Core 应用的大多数请求都可以由调用必要服务的控制器或页面模型处理并返回 HTTP 响应。 对于某些涉及长时间运行的任务的请求，最好使整个请求-响应过程异步。

建议：

* **不要**等待长时间运行的任务作为普通 HTTP 请求处理的一部分完成。
* **请考虑**使用[后台服务](xref:fundamentals/host/hosted-services)处理长时间运行的请求，或者使用[Azure 函数](/azure/azure-functions/)处理进程外的请求。 在进程外完成工作对 CPU 密集型任务特别有利。
* **请使用**实时通信选项（如[SignalR](xref:signalr/introduction)）以异步方式与客户端通信。

## <a name="minify-client-assets"></a>将客户资产进行最小化

ASP.NET具有复杂前端的核心应用经常提供许多 JavaScript、CSS 或图像文件。 初始加载请求的性能可以通过：

* 捆绑，它将多个文件合并为一个文件。
* 明量化，通过删除空格和注释来减小文件的大小。

建议：

* **使用**ASP.NET Core 的[内置支持](xref:client-side/bundling-and-minification)来捆绑和美化客户资产。
* **请考虑**其他第三方工具，如[Webpack，](https://webpack.js.org/)用于复杂的客户端资产管理。

## <a name="compress-responses"></a>压缩响应

 减小响应大小通常会增加应用程序的响应能力，通常显著。 减少有效负载大小的一种方法是压缩应用的响应。 有关详细信息，请参阅[响应压缩](xref:performance/response-compression)。

## <a name="use-the-latest-aspnet-core-release"></a>使用最新的ASP.NET核心版本

每个新版本ASP.NET核心包括性能改进。 .NET Core 和 ASP.NET 核心中的优化意味着较新版本的性能通常优于旧版本。 例如，.NET Core 2.1 添加了对编译正则表达式的支持，并从[span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx)中获益。 ASP.NET核心 2.2 添加了对 HTTP/2 的支持。 [ASP.NET Core 3.0 添加了许多改进](xref:aspnetcore-3.0)，以减少内存使用并提高吞吐量。 如果性能是重中之重，请考虑升级到当前版本的 ASP.NET 酷睿。

## <a name="minimize-exceptions"></a>最小化异常

异常应该很少见。 与其他代码流模式相比，引发和捕获异常的速度很慢。 因此，不应使用异常来控制正常程序流。

建议：

* **请勿**使用引发或捕获异常作为正常程序流的手段，尤其是在[热代码路径](#understand-hot-code-paths)中。
* **在**应用中是否包括逻辑，以检测和处理会导致异常的条件。
* 对于异常或意外的情况，**一应**引发或捕获异常。

应用诊断工具（如应用程序见解）可帮助识别应用中可能影响性能的常见异常。

## <a name="performance-and-reliability"></a>性能和可靠性

以下各节提供性能提示和已知可靠性问题和解决方案。

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>避免在 HttpRequest/HttpResponse 正文上同步读取或写入

ASP.NET核心中的所有 I/O 都是异步的。 服务器实现`Stream`接口，该接口具有同步和异步重载。 应首选异步线程以避免阻塞线程池线程。 阻塞线程可能导致线程池不足。

**不要这样做：** 下面的示例使用 。 <xref:System.IO.StreamReader.ReadToEnd*> 它阻止当前线程等待结果。 这是[一个通过异步同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)的示例。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

在前面的代码中，`Get`同步将整个 HTTP 请求正文读取到内存中。 如果客户端正在缓慢上载，则应用正在通过异步进行同步。 应用程序会同步，因为 Kestrel**不支持**同步读取。

**执行此操作：** 下面的示例使用<xref:System.IO.StreamReader.ReadToEndAsync*>并且不会在读取时阻止线程。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

前面的代码异步地将整个 HTTP 请求正文读取到内存中。

> [!WARNING]
> 如果请求很大，将整个 HTTP 请求正文读取到内存中可能会导致内存不足 （OOM） 条件。 OOM 可能会导致拒绝服务。  有关详细信息，请参阅本文档中[避免将大型请求正文或响应正文读取到内存](#arlb)中。

**执行此操作：** 以下示例使用非缓冲请求正文完全异步：

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

前面的代码异步将请求正文序列化为 C# 对象。

## <a name="prefer-readformasync-over-requestform"></a>首选阅读形式同步而不是请求.窗体

使用 `HttpContext.Request.ReadFormAsync` 而非 `HttpContext.Request.Form`。
`HttpContext.Request.Form`只能在以下条件下安全读取：

* 表单已通过调用 和`ReadFormAsync`
* 正在使用`HttpContext.Request.Form`

**不要这样做：** 下面的示例使用`HttpContext.Request.Form`。  `HttpContext.Request.Form`[使用异步同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)，并可能导致线程池不足。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**执行此操作：** 下面的示例用于`HttpContext.Request.ReadFormAsync`异步读取窗体正文。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>避免将大型请求正文或响应正文读取到内存中

在 .NET 中，每个大于 85 KB 的对象分配最终都位于大型对象堆[（LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)） 中。 大型对象在两个方面非常昂贵：

* 分配成本很高，因为必须清除新分配的大型对象的内存。 CLR 保证清除所有新分配对象的内存。
* LOH 与堆的其余部分一起收集。 LOH 需要完整的[垃圾回收](/dotnet/standard/garbage-collection/fundamentals)或[第 2 代收集](/dotnet/standard/garbage-collection/fundamentals#generations)。

这个[博客文章](https://adamsitnik.com/Array-Pool/#the-problem)简明扼要地描述了这个问题：

> 分配大型对象时，将将其标记为第 2 代对象。 对于小对象，不是第 0 代。 其后果是，如果内存在 LOH 中耗尽，GC 将清理整个托管堆，而不仅仅是 LOH。 因此，它清理第 0 代、第 1 代和第 2 代（包括 LOH）。 这称为完全垃圾回收，是最耗时的垃圾回收。 对于许多应用程序，这是可以接受的。 但绝对不是高性能 Web 服务器，因为处理普通 Web 请求只需要很少大内存缓冲区（从套接字读取、解压缩、解码 JSON &更多）。

天真地将大型请求或响应正文存储到单个`byte[]`或`string`：

* 可能导致 LOH 中空间迅速耗尽。
* 由于完全运行了 DC，可能会导致应用的性能问题。

## <a name="working-with-a-synchronous-data-processing-api"></a>使用同步数据处理 API

使用仅支持同步读取和写入的序列化器/去序列化器时（例如[，JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)）：

* 将数据异步缓冲到内存中，然后再将其传递到序列化器/去序列化器中。

> [!WARNING]
> 如果请求很大，则可能导致内存不足 （OOM） 条件。 OOM 可能会导致拒绝服务。  有关详细信息，请参阅本文档中[避免将大型请求正文或响应正文读取到内存](#arlb)中。

默认情况下，ASP.NET核心 3.0 用于<xref:System.Text.Json>JSON 序列化。 <xref:System.Text.Json>:

* 以异步方式读取和写入 JSON。
* 针对 UTF-8 文本进行了优化。
* 通常比 `Newtonsoft.Json` 性能更高。

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>不要将 IHttpContextAccessor.httpContext 存储在字段中

从请求线程访问时[，IHttpContextAccessor.httpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)`HttpContext`返回活动请求。 `IHttpContextAccessor.HttpContext`**不应**存储在字段或变量中。

**不要这样做：** 下面的示例将 存储在`HttpContext`字段中，然后尝试稍后使用它。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

前面的代码经常在构造函数中捕获空或不正确`HttpContext`。

**执行此操作：** 以下示例：

* 将<xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>存储在字段中。
* 在正确的`HttpContext`时间使用该字段并检查`null`。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>不要从多个线程访问 HttpContext

`HttpContext`*不是*线程安全的。 并行`HttpContext`从多个线程访问可能会导致未定义的行为，如挂起、崩溃和数据损坏。

**不要这样做：** 下面的示例发出三个并行请求，并在传出 HTTP 请求之前和之后记录传入的请求路径。 请求路径从多个线程访问，可能并行访问。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**执行此操作：** 以下示例在发出三个并行请求之前复制来自传入请求的所有数据。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>请求完成后不要使用 HttpContext

`HttpContext`仅当ASP.NET核心管道中有活动 HTTP 请求时，才有效。 整个ASP.NET核心管道是执行每个请求的异步委托链。 当`Task`从此链返回完成时，将回收`HttpContext`。

**不要这样做：** 以下示例使用`async void`使 HTTP 请求在到达第一个`await`请求时完成：

* 这在ASP.NET核心应用程序中**总是**一个坏的做法。
* 访问`HttpResponse`HTTP 请求完成后的 。
* 崩溃进程。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**执行此操作：** 下面的示例返回 到`Task`框架，因此 HTTP 请求在操作完成之前不会完成。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>不要在后台线程中捕获 HttpContext

**不要这样做：** 下面的示例显示闭包正在从 属性捕获`HttpContext`。 `Controller` 这是一个糟糕的做法，因为工作项可以：

* 在请求范围之外运行。
* 试图读错`HttpContext`。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**执行此操作：** 以下示例：

* 复制请求期间后台任务中所需的数据。
* 不引用控制器的任何内容。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

后台任务应作为托管服务实现。 有关详细信息，请参阅[使用托管服务的后台任务](xref:fundamentals/host/hosted-services)。

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>不要捕获注入后台线程控制器的服务

**不要这样做：** 下面的示例显示闭包正在从`DbContext``Controller`操作参数捕获 。 这是一个坏的做法。  工作项可以运行在请求范围之外。 `ContosoDbContext`的范围限定到请求，从而导致 。 `ObjectDisposedException`

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**执行此操作：** 以下示例：

* 注入<xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>，以便在后台工作项中创建作用域。 `IServiceScopeFactory`是一个单一的。
* 在后台线程中创建新的依赖项注入作用域。
* 不引用控制器的任何内容。
* 不从传入请求捕获`ContosoDbContext`。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

以下突出显示的代码：

* 为后台操作的生存期创建一个作用域，并从中解析服务。
* 从`ContosoDbContext`正确的作用域使用。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>响应正文启动后，不要修改状态代码或标头

ASP.NET核心不会缓冲 HTTP 响应正文。 首次编写响应：

* 标头与正文的块一起发送到客户端。
* 不再可以更改响应标头。

**不要这样做：** 以下代码尝试在响应启动后添加响应标头：

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

在前面的代码中，`context.Response.Headers["test"] = "test value";`如果`next()`已写入响应，将引发异常。

**执行此操作：** 以下示例检查 HTTP 响应在修改标头之前是否已启动。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**执行此操作：** 以下示例用于`HttpResponse.OnStarting`在响应标头刷新到客户端之前设置标头。

检查响应是否尚未启动，可以注册将在编写响应标头之前调用的回调。 检查响应是否尚未启动：

* 提供及时追加或覆盖标头的能力。
* 不需要了解管道中的下一个中间件。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>如果您已经开始写入响应正文，请不要调用下一个（）

仅当组件可以处理和操作响应时，才期望调用组件。
