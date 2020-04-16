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
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>ASP.NET核心中的内存管理和垃圾回收 （GC）

由[塞巴斯蒂安·罗斯](https://github.com/sebastienros)和[里克·安德森](https://twitter.com/RickAndMSFT)

内存管理很复杂，即使在像 .NET 这样的托管框架中也是如此。 分析和理解内存问题可能具有挑战性。 本文：

* 受许多*内存泄漏*和*GC 不工作*问题引起的。 这些问题大多是由于不了解内存消耗在 .NET Core 中的工作方式，或者不了解如何测量内存消耗造成的。
* 演示有问题的内存使用，并建议替代方法。

## <a name="how-garbage-collection-gc-works-in-net-core"></a>垃圾回收 （GC） 在 .NET 核心中的工作方式

GC 分配堆段，其中每个段是连续的内存范围。 放置在堆中的对象分为 3 代之一：0、1 或 2。 生成确定 GC 尝试释放应用程序不再引用的托管对象上的内存的频率。 编号较低的代是 GC 更频繁地使用。

对象根据其生存期从一代移动到另一代。 随着对象寿命的延长，它们被移动到更高的一代。 如前所述，较高一代是 GC 的较少频率。 短期生存对象始终保留在第 0 代中。 例如，在 Web 请求期间引用的对象是短暂的。 应用程序级[单例](xref:fundamentals/dependency-injection#service-lifetimes)通常迁移到第 2 代。

当 ASP.NET 核心应用启动时，GC：

* 为初始堆段保留一些内存。
* 加载运行时时提交一小部分内存。

出于性能原因，上述内存分配完成。 性能优势来自连续内存中的堆段。

### <a name="call-gccollect"></a>调用 GC。收集

调用[GC。显式收集](xref:System.GC.Collect*)：

* **不应**通过生产ASP.NET核心应用来完成。
* 在调查内存泄漏时很有用。
* 调查时，验证 GC 从内存中删除了所有悬空对象，以便可以测量内存。

## <a name="analyzing-the-memory-usage-of-an-app"></a>分析应用的内存使用情况

专用工具可帮助分析内存使用情况：

- 计数对象引用
- 测量 GC 对 CPU 使用率的影响
- 测量每一代使用的内存空间

使用以下工具分析内存使用情况：

* [点网跟踪](/dotnet/core/diagnostics/dotnet-trace)：可用于生产机器。
* [分析不使用 Visual Studio 调试器情况下的内存使用情况](/visualstudio/profiling/memory-usage-without-debugging2)
* [Visual Studio 中的配置文件内存使用](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>检测内存问题

任务管理器可用于了解应用使用的ASP.NET内存量。 任务管理器内存值：

* 表示ASP.NET进程使用的内存量。
* 包括应用的活对象和其他内存使用者，如本机内存使用情况。

如果任务管理器内存值无限增加且从不变展，则应用会泄漏内存。 以下各节演示并解释几种内存使用模式。

## <a name="sample-display-memory-usage-app"></a>示例显示内存使用情况应用

[内存泄漏示例应用](https://github.com/sebastienros/memoryleak)在 GitHub 上可用。 内存泄漏应用：

* 包括一个诊断控制器，用于收集应用的实时内存和 GC 数据。
* 具有显示内存和 GC 数据的索引页。 每秒刷新一次索引页。
* 包含一个 API 控制器，该控制器提供各种内存加载模式。
* 不是受支持的工具，但是，它可用于显示ASP.NET核心应用的内存使用模式。

运行内存泄漏。 分配的内存会缓慢增加，直到发生 GC。 内存增加，因为该工具分配自定义对象以捕获数据。 下图显示了发生第 0 代 GC 时内存泄漏索引页。 该图表显示 0 RPS（每秒请求），因为未调用来自 API 控制器的 API 终结点。

![前一图表](memory/_static/0RPS.png)

图表显示内存使用情况的两个值：

- 已分配：托管对象占用的内存量
- [工作集](/windows/win32/memory/working-set)：进程虚拟地址空间中的页面集，当前驻留在物理内存中。 显示的工作集与任务管理器显示的值相同。

### <a name="transient-objects"></a>瞬态对象

以下 API 创建一个 10 KB 字符串实例并将其返回到客户端。 在每个请求上，一个新对象在内存中分配并写入响应。 字符串在 .NET 中存储为 UTF-16 字符，因此每个字符在内存中需要 2 个字节。

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

以下图形的负载相对较小，以显示内存分配如何受到 GC 的影响。

![前一图表](memory/_static/bigstring.png)

上图显示：

* 4K RPS（每秒请求）。
* 第 0 代 GC 集合大约每两秒发生一次。
* 工作集保持不变，约为 500 MB。
* CPU 为 12%。
* 内存消耗和释放（通过 GC）稳定。

下图以机器可以处理的最大吞吐量进行。

![前一图表](memory/_static/bigstring2.png)

上图显示：

* 22K RPS
* 第 0 代 GC 集合每秒发生多次。
* 第 1 代集合被触发，因为应用每秒分配的内存显著增加。
* 工作集保持不变，约为 500 MB。
* CPU 为 33%。
* 内存消耗和释放（通过 GC）稳定。
* CPU （33%）未过度使用，因此垃圾回收可以跟上大量分配。

### <a name="workstation-gc-vs-server-gc"></a>工作站 GC 与服务器 GC

.NET 垃圾回收器有两种不同的模式：

* **工作站 GC**：针对桌面进行了优化。
* **服务器 GC**。 ASP.NET核心应用的默认 GC。 针对服务器进行了优化。

GC 模式可以在项目文件或已发布应用程序的*运行时 config.json*文件中显式设置。 以下标记显示项目文件中的`ServerGarbageCollection`设置：

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

在`ServerGarbageCollection`项目文件中更改需要重新生成应用。

**注：** 服务器垃圾回收在具有单个内核的计算机上**不可用**。 有关详细信息，请参阅 <xref:System.Runtime.GCSettings.IsServerGC>。

下图显示了使用工作站 GC 的 5K RPS 下的内存配置文件。

![前一图表](memory/_static/workstation.png)

此图表和服务器版本之间的差异很大：

- 工作集从 500 MB 下降到 70 MB。
- GC 每秒生成数次集合，而不是每两秒生成一次。
- GC 从 300 MB 下降到 10 MB。

在典型的 Web 服务器环境中，CPU 使用率比内存更重要，因此服务器 GC 更好。 如果内存利用率高且 CPU 使用率相对较低，则工作站 GC 可能更具有性能。 例如，高密度托管多个内存不足的 Web 应用。

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>使用 Docker 和小型容器的 GC

当在一台计算机上运行多个容器化应用时，工作站 GC 可能比服务器 GC 更具预制功能。 有关详细信息，请参阅[在小型容器中使用服务器 GC 运行](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/)，并在[小型容器方案第 1 部分中使用服务器 GC 运行 = GC 堆的硬限制](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)。

### <a name="persistent-object-references"></a>持久对象引用

GC 无法释放引用的对象。 引用但不再需要的对象会导致内存泄漏。 如果应用经常分配对象，并且在不再需要对象后无法释放它们，则内存使用量将随着时间的推移而增加。

以下 API 创建一个 10 KB 字符串实例并将其返回到客户端。 与上一个示例的区别是此实例由静态成员引用，这意味着它永远不会可用于收集。

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

前面的代码：

* 是典型的内存泄漏的示例。
* 频繁调用时，会导致应用内存增加，直到进程崩溃，`OutOfMemory`出现异常。

![前一图表](memory/_static/eternal.png)

在前面的图像中：

* 负载测试`/api/staticstring`终结点会导致内存线性增加。
* GC 尝试通过调用第 2 代集合来释放内存，因为内存压力增大。
* GC 无法释放泄漏的内存。 分配和工作集会随时间而增加。

某些方案（如缓存）要求保持对象引用，直到内存压力强制释放它们。 类<xref:System.WeakReference>可用于这种类型的缓存代码。 对象`WeakReference`是在内存压力下收集的。 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>使用的`WeakReference`默认实现。

### <a name="native-memory"></a>本机内存

某些 .NET Core 对象依赖于本机内存。 GC**无法**收集本机内存。 使用本机内存的 .NET 对象必须使用本机代码释放它。

.NET 提供<xref:System.IDisposable>允许开发人员释放本机内存的接口。 即使<xref:System.IDisposable.Dispose*>未调用，在[终结器](/dotnet/csharp/programming-guide/classes-and-structs/destructors)运行时，也会`Dispose`正确实现类调用。

考虑下列代码：

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[物理文件提供程序](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0)是托管类，因此将在请求结束时收集任何实例。

下图显示连续调用 API 时的`fileprovider`内存配置文件。

![前一图表](memory/_static/fileprovider.png)

前面的图表显示了此类实现的一个明显问题，因为它不断增加内存使用量。 这是一个已知的问题，正在跟踪[在此问题](https://github.com/dotnet/aspnetcore/issues/3110)。

用户代码中也可能发生相同的泄漏，其原因之一如下：

* 未正确释放类。
* 忘记调用应释放的`Dispose`从属对象的方法。

### <a name="large-objects-heap"></a>大型对象堆

频繁的内存分配/空闲周期可能会分散内存，尤其是在分配大块内存时。 对象以连续的内存块分配。 为了缓解碎片，当 GC 释放内存时，它会尝试对它进行碎片整理。 此过程称为**压缩**。 压实涉及移动对象。 移动大型对象会造成性能损失。 因此，GC 会为_大型_对象（称为[大型对象堆](/dotnet/standard/garbage-collection/large-object-heap)（LOH） 创建特殊内存区域。 大于 85，000 字节（约 83 KB）的对象是：

* 放置在 LOH 上。
* 未压缩。
* 在第 2 代 GC 期间收集。

当 LOH 已满时，GC 将触发第 2 代集合。 第 2 代集合：

* 本质上是缓慢的。
* 此外，还要承担触发所有其他代的集合的成本。

以下代码可立即压缩 LOH：

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

有关<xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>压缩 LOH 的信息，请参阅。

在使用 .NET Core 3.0 及更高版本的容器中，LOH 会自动压缩。

以下说明此行为的 API：

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

下图显示了在最大负载下调用终结点的`/api/loh/84975`内存配置文件：

![前一图表](memory/_static/loh1.png)

下图显示了调用终结点的`/api/loh/84976`内存配置文件，仅分配*了一个字节*：

![前一图表](memory/_static/loh2.png)

注意：结构`byte[]`具有开销字节。 这就是为什么 84，976 字节触发 85，000 限制的原因。

比较前面的两个图表：

* 这两种情况的工作集都类似，大约 450 MB。
* LOH 下的请求（84，975 字节）主要显示第 0 代集合。
* 过 LOH 请求生成恒定的第 2 代集合。 第 2 代集合成本高昂。 需要更多的 CPU，吞吐量下降近 50%。

临时大型对象特别成问题，因为它们会导致第 2 代 GC。

为了达到最佳性能，应尽量减少大型对象的使用。 如果可能，拆分大型对象。 例如，ASP.NET Core 中的[响应缓存](xref:performance/caching/response)中间件将缓存条目拆分为小于 85，000 字节的块。

以下链接显示了将对象保持在 LOH 限制下的ASP.NET核心方法：

* [响应缓存/流/流实用程序。cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [响应缓存/内存响应缓存.cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

有关详细信息，请参见:

* [未覆盖的大对象堆](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [大型对象堆](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

使用<xref:System.Net.Http.HttpClient>不当可能会导致资源泄漏。 系统资源，如数据库连接、套接字、文件句柄等：

* 比记忆更稀缺。
* 泄漏时比内存问题更大。

经验丰富的 .NET 开发人员知道<xref:System.IDisposable.Dispose*>调用实现<xref:System.IDisposable>的对象。 不释放实现`IDisposable`的对象通常会导致内存泄漏或系统资源泄漏。

`HttpClient`实现`IDisposable`，但**不应**在每一次调用时都处置。 相反，`HttpClient`应该重复使用。

以下终结点在每个请求上创建并释放一`HttpClient`个新实例：

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

在负载下，将记录以下错误消息：

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

即使`HttpClient`实例已释放，操作系统也会释放实际网络连接。 通过不断创建新连接，_端口耗尽_。 每个客户端连接都需要其自己的客户端端口。

防止端口耗尽的一种方法是重用同一`HttpClient`实例：

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

`HttpClient`当应用停止时，实例将被释放。 此示例显示，在每次使用后，不应释放每个一次性资源。

有关处理`HttpClient`实例生存期的更好方法，请参阅以下内容：

* [HttpClient 和生存期管理](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [HTTPClient 工厂博客](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>对象池

前面的示例演示如何使`HttpClient`实例成为静态的，并被所有请求重用。 重用可防止资源耗尽。

对象池：

* 使用重用模式。
* 专为创建成本高昂的对象而设计。

池是预初始化对象的集合，可以在线程之间保留和释放。 池可以定义分配规则，如限制、预定义大小或增长率。

NuGet 包[Microsoft.扩展.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/)包含有助于管理此类池的类。

以下 API 终结点实例化了`byte`在每个请求上填充随机数的缓冲区：

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

下图显示了使用中等负载调用前面的 API 的图表显示：

![前一图表](memory/_static/array.png)

在前面的图表中，第 0 代集合大约每秒发生一次。

可以通过使用[ArrayPool\<T>](xref:System.Buffers.ArrayPool`1)`byte`池化缓冲区来优化前面的代码。 静态实例跨请求重用。

此方法的不同做法是从 API 返回池对象。 这意味着：

* 从 方法返回时，对象将失去控制。
* 无法释放对象。

要设置对象的处置：

* 将池数组封装在一次性对象中。
* 使用[HttpContext.Response.注册为 Dispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)注册池对象。

`RegisterForDispose`将负责对目标对象的`Dispose`调用，以便仅在 HTTP 请求完成时释放它。

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

应用与非池式版本相同的负载会导致以下图表：

![前一图表](memory/_static/pooledarray.png)

主要区别是分配字节，因此，第 0 代集合要少得多。

## <a name="additional-resources"></a>其他资源

* [垃圾回收](/dotnet/standard/garbage-collection/)
* [使用并发可视化工具了解不同的 GC 模式](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [未覆盖的大对象堆](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [大型对象堆](/dotnet/standard/garbage-collection/large-object-heap)
