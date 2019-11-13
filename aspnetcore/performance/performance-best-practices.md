---
title: ASP.NET Core 性能最佳做法
author: mjrousos
description: 在 ASP.NET Core 应用中提高性能并避免出现常见性能问题的提示。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/12/2019
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 279bf352580e5e45fc005e800ee536871210409b
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963247"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Core 性能最佳做法

作者： [Mike Rousos](https://github.com/mjrousos)

本文提供了有关 ASP.NET Core 的性能最佳做法的准则。

## <a name="cache-aggressively"></a>主动缓存

此文档的几个部分讨论了缓存。 有关更多信息，请参见<xref:performance/caching/response>。

## <a name="understand-hot-code-paths"></a>了解热代码路径

在本文档中，将*热代码路径*定义为经常调用的代码路径和执行时间量。 热代码路径通常限制应用向外缩放和性能，并将在本文档的几个部分中进行讨论。

## <a name="avoid-blocking-calls"></a>避免阻止调用

应将 ASP.NET Core 应用程序设计为同时处理许多请求。 异步 Api 允许一小部分线程通过不等待阻止调用来处理上千个并发请求。 线程可以处理另一请求，而不是等待长时间运行的同步任务完成。

ASP.NET Core 应用中的常见性能问题是阻止可能是异步的调用。 很多同步阻塞调用会导致[线程池](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)不足并降低响应时间。

**请勿**：

* 通过调用[task. Wait](/dotnet/api/system.threading.tasks.task.wait)或[task.](/dotnet/api/system.threading.tasks.task-1.result)来阻止异步执行。
* 获取通用代码路径中的锁。 当构建为并行运行代码时，ASP.NET Core 应用程序的性能最高。

**Do**：

* 使[热代码路径](#understand-hot-code-paths)处于异步状态。
* 以异步方式调用数据访问和长时间运行的操作 Api。
* 使控制器/Razor 页面操作异步。 为了受益于[async/await](/dotnet/csharp/programming-guide/concepts/async/)模式，整个调用堆栈是异步的。

探查器（如[PerfView](https://github.com/Microsoft/perfview)）可用于查找频繁添加到[线程池中](/windows/desktop/procthread/thread-pools)的线程。 `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` 事件表示添加到线程池中的线程。 <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>最小化大型对象分配

[.Net Core 垃圾回收器](/dotnet/standard/garbage-collection/)在 ASP.NET Core 应用中自动管理内存的分配和释放。 自动垃圾回收通常意味着开发人员无需担心如何或何时释放内存。 但是，清理未引用的对象会占用 CPU 时间，因此开发人员应最大限度地减少[热代码路径](#understand-hot-code-paths)中的对象分配。 垃圾回收对于大型对象（> 85 K 字节）特别昂贵。 大型对象存储在[大型对象堆](/dotnet/standard/garbage-collection/large-object-heap)上，并要求进行完整（第2代）垃圾回收。 与第0代和第1代回收不同，第2代回收需要临时暂停应用执行。 频繁分配和取消分配大型对象会导致性能不一致。

针对

* **请考虑缓存**经常使用的大型对象。 缓存大型对象会阻止开销较高的分配。
* 使用用于存储大型数组的[`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1) **来池缓冲区**。
* **不要**在[热代码路径](#understand-hot-code-paths)上分配很多生存期较短的大型对象。

可以通过查看[PerfView](https://github.com/Microsoft/perfview)中的垃圾回收（GC）统计信息并进行检查来诊断内存问题，例如前面的问题：

* 垃圾回收暂停时间。
* 垃圾回收所用的处理器时间百分比。
* 第0代、第1代和第2代垃圾回收量。

有关详细信息，请参阅[垃圾回收和性能](/dotnet/standard/garbage-collection/performance)。

## <a name="optimize-data-access"></a>优化数据访问

与数据存储和其他远程服务的交互通常是 ASP.NET Core 应用程序的最慢部分。 有效读取和写入数据对于良好的性能至关重要。

针对

* **请**以异步方式调用所有数据访问 api。
* 检索的数据**不**是必需的。 编写查询以仅返回当前 HTTP 请求所必需的数据。
* 如果数据可以接受，**请考虑缓存**经常访问的从数据库或远程服务检索的数据。 使用[MemoryCache](xref:performance/caching/memory)或[microsoft.web.distributedcache](xref:performance/caching/distributed)，具体取决于方案。 有关更多信息，请参见<xref:performance/caching/response>。
* **尽量减少**网络往返次数。 目标是使用单个调用而不是多个调用来检索所需数据。
* 在访问数据时，**请不要**在 Entity Framework Core 中使用[无跟踪查询](/ef/core/querying/tracking#no-tracking-queries)。 EF Core 可以更有效地返回无跟踪查询的结果。
* **筛选和**聚合 LINQ 查询（例如，使用 `.Where`、`.Select`或 `.Sum` 语句），以便数据库执行筛选。
* **请考虑 EF Core**在客户端上解析一些查询运算符，这可能导致查询执行效率低下。 有关详细信息，请参阅[客户端评估性能问题](/ef/core/querying/client-eval#client-evaluation-performance-issues)。
* **不要**对集合使用投影查询，这可能会导致执行 "N + 1" 个 SQL 查询。 有关详细信息，请参阅[相关子查询的优化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)。

请参阅[EF 高性能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)，了解可提高大规模应用程序性能的方法：

* [DbContext 池](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [显式编译的查询](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

建议在提交基本代码之前测量前面的高性能方法的影响。 已编译查询的额外复杂性可能不会提高性能。

通过查看[Application Insights](/azure/application-insights/app-insights-overview)或分析工具访问数据所用的时间，可以检测到查询问题。 大多数数据库还提供有关频繁执行的查询的统计信息。

## <a name="pool-http-connections-with-httpclientfactory"></a>与 HttpClientFactory 建立池 HTTP 连接

尽管[HttpClient](/dotnet/api/system.net.http.httpclient)实现 `IDisposable` 接口，但它是为重复使用而设计的。 关闭 `HttpClient` 实例会使套接字在一小段时间内打开 `TIME_WAIT` 状态。 如果经常使用创建和释放 `HttpClient` 对象的代码路径，应用程序可能会耗尽可用的套接字。 ASP.NET Core 2.1 中引入了[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)作为此问题的解决方案。 它处理池 HTTP 连接以优化性能和可靠性。

针对

* **不要**直接创建和释放 `HttpClient` 实例。
* **请**使用[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)检索 `HttpClient` 实例。 有关详细信息，请参阅[使用 HttpClientFactory 实现复原 HTTP 请求](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)。

## <a name="keep-common-code-paths-fast"></a>快速保持通用代码路径

您希望所有的代码都是快速的，通常称为代码路径是最重要的，可进行优化：

* 应用程序的请求处理管道中的中间件组件，尤其是在管道早期运行的中间件。 这些组件会对性能产生很大的影响。
* 针对每个请求或每个请求多次执行的代码。 例如，自定义日志记录、授权处理程序或暂时性服务的初始化。

针对

* **不要**将自定义中间件组件用于长时间运行的任务。
* **请使用性能**分析工具（如[Visual Studio 诊断工具](/visualstudio/profiling/profiling-feature-tour)或[PerfView](https://github.com/Microsoft/perfview)）来识别[热代码路径](#understand-hot-code-paths)。

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>在 HTTP 请求之外完成长时间运行的任务

大多数对 ASP.NET Core 应用程序的请求都可以通过控制器或页面模型进行处理，该模型调用必要的服务并返回 HTTP 响应。 对于涉及长时间运行的任务的某些请求，最好将整个请求响应过程设为异步处理。

针对

* **请**不要等待长时间运行的任务在普通的 HTTP 请求处理过程中完成。
* **请考虑使用**[后台服务](xref:fundamentals/host/hosted-services)处理长时间运行的请求，或使用[Azure 函数](/azure/azure-functions/)处理进程外的请求。 在进程外完成工作对于 CPU 密集型任务特别有用。
* **请使用实时**通信选项（如[SignalR](xref:signalr/introduction)）以异步方式与客户端进行通信。

## <a name="minify-client-assets"></a>缩小客户端资产

具有复杂前端的 ASP.NET Core 应用通常会提供许多 JavaScript、CSS 或图像文件。 可以通过以下方式改善初始负载请求的性能：

* 绑定，将多个文件合并到一个文件中。
* 缩小，它通过删除空白和注释来减小文件大小。

针对

* **请**使用 ASP.NET Core 的[内置支持](xref:client-side/bundling-and-minification)，以便对客户端资产进行捆绑和缩小。
* **请考虑其他**第三方工具（如[Webpack](https://webpack.js.org/)），以实现复杂的客户端资产管理。

## <a name="compress-responses"></a>压缩响应

 减小响应大小通常会显著提高应用程序的响应能力。 减少负载大小的一种方法是压缩应用的响应。 有关详细信息，请参阅[响应压缩](xref:performance/response-compression)。

## <a name="use-the-latest-aspnet-core-release"></a>使用最新 ASP.NET Core 版本

ASP.NET Core 的每个新版本都包括性能改进。 .NET Core 和 ASP.NET Core 中的优化意味着较新版本通常优于较旧的版本。 例如，.NET Core 2.1 添加了对[`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx)中已编译的正则表达式和获益的支持。 ASP.NET Core 2.2 添加了对 HTTP/2 的支持。 [ASP.NET Core 3.0 添加了许多改进](xref:aspnetcore-3.0)，减少了内存使用量并提高了吞吐量。 如果性能是优先考虑的，请考虑升级到 ASP.NET Core 的当前版本。

## <a name="minimize-exceptions"></a>最小化异常

异常应极少。 相对于其他代码流模式，引发和捕获异常的速度很慢。 因此，不应使用异常来控制正常的程序流。

针对

* **不要**使用引发或捕获异常作为正常程序流的方法，尤其是在[热代码路径](#understand-hot-code-paths)中。
* 在应用程序**中包括逻辑**，以检测和处理会导致异常的情况。
* **引发或**捕获异常或意外情况的异常。

应用诊断工具（如 Application Insights）可帮助识别应用中可能影响性能的常见异常。

## <a name="performance-and-reliability"></a>性能和可靠性

以下各节提供了性能提示以及已知的可靠性问题和解决方案。

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>避免 HttpRequest/Httpresponse.cache 正文上的同步读取或写入

ASP.NET Core 中的所有 IO 都是异步的。 服务器实现 `Stream` 接口，该接口具有同步和异步重载。 应首选异步文件以避免阻塞线程池线程。 阻塞线程可能会导致线程池不足。

请勿**执行此操作：** 下面的示例使用 <xref:System.IO.StreamReader.ReadToEnd*>。 此方法阻止当前线程等待结果。 这是一个[通过异步同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)的示例。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

在前面的代码中，`Get` 以同步方式将整个 HTTP 请求正文读入内存中。 如果客户端缓慢上传，则应用通过异步执行同步。 应用通过异步同步，因为 Kestrel**不支持同步**读取。

**执行以下操作：** 下面的示例使用 <xref:System.IO.StreamReader.ReadToEndAsync*>，在读取时不会阻止线程。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

前面的代码异步将整个 HTTP 请求正文读入内存中。

> [!WARNING]
> 如果请求很大，则将整个 HTTP 请求正文读取到内存中可能会导致内存不足（OOM）。 OOM 可能会导致拒绝服务。  有关详细信息，请参阅本文档中的[避免将大型请求正文或响应正文读入内存](#arlb)中。

**执行以下操作：** 下面的示例使用非缓冲请求正文完全异步：

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

前面的代码将请求正文异步反序列化为C#对象。

## <a name="prefer-readformasync-over-requestform"></a>首选 ReadFormAsync over 请求。窗体

使用 `HttpContext.Request.ReadFormAsync` 而非 `HttpContext.Request.Form`。
仅在以下情况下，才能安全地读取 `HttpContext.Request.Form`：

* 已通过调用 `ReadFormAsync`读取了窗体，且
* 正在使用读取缓存的窗体值 `HttpContext.Request.Form`

请勿**执行此操作：** 下面的示例使用 `HttpContext.Request.Form`。  `HttpContext.Request.Form`[通过异步使用同步](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)，可能会导致线程池不足。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**执行以下操作：** 下面的示例使用 `HttpContext.Request.ReadFormAsync` 以异步方式读取窗体体。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>避免将大型请求正文或响应正文读入内存

在 .NET 中，大于 85 KB 的每个对象分配将在大型对象堆（[LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)）中结束。 大型对象的开销很大：

* 分配开销较高，因为必须清除新分配的大型对象的内存。 CLR 确保清除所有新分配对象的内存。
* LOH 随堆的其余部分一起收集。 LOH 需要完整的[垃圾回收](/dotnet/standard/garbage-collection/fundamentals)或[Gen2 集合](/dotnet/standard/garbage-collection/fundamentals#generations)。

此[博客文章](https://adamsitnik.com/Array-Pool/#the-problem)简单介绍了问题：

> 分配大型对象时，会将其标记为第2代对象。 对于小对象，不是0代。 后果是，如果在 LOH 中用尽内存，GC 将清除整个托管堆，而不仅是 LOH。 因此，它会清除第0代第1代和第2代，包括 LOH。 这称为完整垃圾回收，是最耗费时间的垃圾回收。 许多应用程序都可以接受。 但一定不要用于高性能的 web 服务器，在这种情况下，需要少量的大内存缓冲区来处理平均 web 请求（从套接字读取、解压缩、解码 JSON & 更多）。

将大型请求或响应正文存储到单个 `byte[]` 或 `string`中的 Naively：

* 可能会导致 LOH 中的空间快速耗尽。
* 可能导致应用程序出现性能问题，因为正在运行完全 Gc。

## <a name="working-with-a-synchronous-data-processing-api"></a>使用同步数据处理 API

使用仅支持同步读和写的序列化程序/反序列化程序（例如， [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)）时：

* 将数据异步缓冲到内存中，然后将其传递给序列化程序/反序列化程序。

> [!WARNING]
> 如果请求很大，则可能导致内存不足（OOM）。 OOM 可能会导致拒绝服务。  有关详细信息，请参阅本文档中的[避免将大型请求正文或响应正文读入内存](#arlb)中。

默认情况下，ASP.NET Core 3.0 使用 <xref:System.Text.Json> 进行 JSON 序列化。 <xref:System.Text.Json>：

* 以异步方式读取和写入 JSON。
* 针对 UTF-8 文本进行了优化。
* 通常比 `Newtonsoft.Json` 性能更高。

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>不要在字段中存储 IHttpContextAccessor

从请求线程访问时， [IHttpContextAccessor](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)将返回活动请求的 `HttpContext`。 `IHttpContextAccessor.HttpContext`**不**应存储在字段或变量中。

请勿**执行此操作：** 下面的示例将 `HttpContext` 存储在字段中，然后稍后尝试使用它。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

前面的代码在构造函数中频繁捕获 null 或不正确的 `HttpContext`。

**执行以下操作：** 下面的示例：

* 将 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 存储在字段中。
* 在正确的时间使用 `HttpContext` 字段并检查 `null`。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>不要从多个线程访问 HttpContext

`HttpContext`*不*是线程安全的。 并行访问来自多个线程的 `HttpContext` 可能会导致未定义的行为，如挂起、崩溃和数据损坏。

请勿**执行此操作：** 下面的示例执行三个并行请求，并在传出 HTTP 请求之前和之后记录传入的请求路径。 可以从多个线程访问请求路径，可能会并行进行。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**执行以下操作：** 下面的示例在发出三个并行请求之前复制传入请求中的所有数据。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>请求完成后，不要使用 HttpContext

只要 ASP.NET Core 管道中存在活动 HTTP 请求，`HttpContext` 才有效。 整个 ASP.NET Core 管道是一系列执行每个请求的委托。 从此链返回的 `Task` 完成后，`HttpContext` 将被回收。

请勿**执行此操作：** 下面的示例使用 `async void`，这会在达到第一个 `await` 时完成 HTTP 请求：

* 在 ASP.NET Core 应用程序中，这**始终**是一种不好的做法。
* HTTP 请求完成后，访问 `HttpResponse`。
* 崩溃进程。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**执行以下操作：** 下面的示例将 `Task` 返回到框架，以便在操作完成之前，不会完成 HTTP 请求。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>不要捕获后台线程中的 HttpContext

请勿**执行此操作：** 下面的示例演示关闭从 `Controller` 属性捕获 `HttpContext`。 这是一种不好的做法，因为工作项可以：

* 在请求范围之外运行。
* 尝试读取错误的 `HttpContext`。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**执行以下操作：** 下面的示例：

* 在请求过程中复制后台任务所需的数据。
* 不从控制器引用任何内容。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

应将后台任务作为托管服务实现。 有关详细信息，请参阅[使用托管服务的后台任务](xref:fundamentals/host/hosted-services)。

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>不要捕获注入到后台线程控制器的服务

请勿**执行此操作：** 下面的示例演示关闭从 `Controller` 操作参数捕获 `DbContext`。 这是一种不好的做法。  工作项可以在请求范围之外运行。 `ContosoDbContext` 的作用域限定为请求，导致 `ObjectDisposedException`。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**执行以下操作：** 下面的示例：

* 注入 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 以便在后台工作项中创建作用域。 `IServiceScopeFactory` 为单一实例。
* 在后台线程中创建新的依赖项注入范围。
* 不从控制器引用任何内容。
* 不捕获传入请求中的 `ContosoDbContext`。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

以下突出显示的代码：

* 在后台操作的生存期内创建一个范围，并从中解析服务。
* 使用来自正确范围的 `ContosoDbContext`。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>请不要在响应正文开始后修改状态代码或标头

ASP.NET Core 不会缓冲 HTTP 响应正文。 第一次写入响应时：

* 标头将与主体块区一起发送到客户端。
* 不能再更改响应标头。

请勿**执行此操作：** 以下代码在响应已启动之后尝试添加响应标头：

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

在前面的代码中，如果 `next()` 已写入响应，则 `context.Response.Headers["test"] = "test value";` 会引发异常。

**执行以下操作：** 下面的示例在修改标头之前检查 HTTP 响应是否已启动。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**执行以下操作：** 下面的示例使用 `HttpResponse.OnStarting` 在将响应标头刷新到客户端之前设置标头。

如果检查响应是否尚未启动，则允许注册将在写入响应标头之前调用的回调。 检查响应是否尚未开始：

* 提供了随时追加或重写标头的功能。
* 不需要了解管道中的下一个中间件。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>如果已开始写入响应正文，则不调用 next （）

仅当组件可以处理和操作响应时，才应调用组件。
