---
title: ASP.NET Core Blazor 高级方案
author: guardrex
description: 了解 Blazor 中的高级方案，包括如何将 RenderTreeBuilder 手动逻辑合并到应用中。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
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
ms.openlocfilehash: 4bd73acd821a8791d7f6cc93545edc2e39a6f2c7
ms.sourcegitcommit: 68d03d1aee8906b53bda66f8f1e0747efc3007e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88051779"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a>ASP.NET Core Blazor 高级方案

作者：[Luke Latham](https://github.com/guardrex) 和 [Daniel Roth](https://github.com/danroth27)

## <a name="no-locblazor-server-circuit-handler"></a>Blazor Server 线路处理程序

Blazor Server 允许代码定义线路处理程序，后者允许在用户线路的状态发生更改时运行代码。 线路处理程序通过从 `CircuitHandler` 派生并在应用的服务容器中注册该类实现。 以下线路处理程序示例跟踪打开的 SignalR 连接：

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

线路处理程序使用 DI 注册。 每个线路实例都会创建区分范围的实例。 借助前面示例中的 `TrackingCircuitHandler` 创建单一实例服务，因为必须跟踪所有线路的状态：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

如果自定义线路处理程序的方法引发未处理异常，则该异常会导致 Blazor Server 线路产生严重错误。 若要容忍处理程序代码或被调用方法中的异常，请使用错误处理和日志记录将代码包装到一个或多个 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句中。

当线路因用户断开连接而结束且框架正在清除线路状态时，框架会处置线路的 DI 范围。 处置范围时会处置所有实现 <xref:System.IDisposable?displayProperty=fullName> 的区分线路范围的 DI 服务。 如果有任何 DI 服务在处置期间引发未处理异常，则框架会记录该异常。

## <a name="manual-rendertreebuilder-logic"></a>RenderTreeBuilder 手动逻辑

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 提供用于操作组件和元素的方法，包括在 C# 代码中手动生成组件。

> [!NOTE]
> 使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 创建组件是一种高级方案。 格式不正确的组件（例如，未封闭的标记标签）可能导致未定义的行为。

考虑以下 `PetDetails` 组件，可将其手动生成到另一个组件中：

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

在以下示例中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。 在具有序列号的 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法中，序列号是源代码行号。 Blazor 差分算法依赖于对应于不同代码行（而不是不同调用的调用）的序列号。 使用 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 方法创建组件时，请对序列号的参数进行硬编码。 **通过计算或计数器生成序列号可能导致性能不佳。** 有关详细信息，请参阅[序列号与代码行号相关，而不与执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)部分。

`BuiltContent` 组件：

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
> <xref:Microsoft.AspNetCore.Components.RenderTree> 中的类型允许处理呈现操作的*结果*。 这些是 Blazor 框架实现的内部细节。 这些类型应视为*不稳定*，并且在未来版本中可能会有更改。

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>序列号与代码行号相关，而不与执行顺序相关

Razor 组件文件 (`.razor`) 始终被编译。 与解释代码相比，编译具有潜在优势，因为编译步骤可用于注入信息，从而在运行时提高应用性能。

这些改进的关键示例涉及*序列号*。 序列号向运行时指示哪些输出来自哪些不同的已排序代码行。 运行时使用此信息在线性时间内生成高效的树上差分，这比常规树上差分算法通常可以做到的速度快得多。

请考虑使用以下 Razor 组件（`.razor` 文件）：

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

前面的代码编译为以下所示内容：

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

首次执行代码时，如果 `someFlag` 为 `true`，则生成器会收到：

| 序列 | 类型      | 数据   |
| :------: | --------- | :----: |
| 0        | Text 节点 | First  |
| 1        | Text 节点 | 秒 |

假设 `someFlag` 变为 `false` 且标记再次呈现。 此时，生成器会收到：

| 序列 | 类型       | 数据   |
| :------: | ---------- | :----: |
| 1        | Text 节点  | 秒 |

当运行时执行差分时，它会看到序列 `0` 处的项目已被删除，因此，它会生成以下普通*编辑脚本*：

* 删除第一个文本节点。

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>以编程方式生成序列号的问题

想象一下，你编写了以下呈现树生成器逻辑：

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

现在，第一个输出是：

| 序列 | 类型      | 数据   |
| :------: | --------- | :----: |
| 0        | Text 节点 | First  |
| 1        | Text 节点 | 秒 |

此结果与之前的示例相同，因此不存在负面问题。 在第二个呈现中，`someFlag` 为 `false`，输出为：

| 序列 | 类型      | 数据   |
| :------: | --------- | ------ |
| 0        | Text 节点 | 秒 |

此时，差分算法发现发生了*两个*变化，且算法生成以下编辑脚本：

* 将第一个文本节点的值更改为 `Second`。
* 删除第二个文本节点。

生成序列号会丢失有关原始代码中 `if/else` 分支和循环的位置的所有有用信息。 这会导致**两倍于**之前长度的差异。

这是一个普通示例。 在具有深度嵌套的复杂结构（尤其是带有循环）的更真实的情况下，性能成本通常会更高。 差分算法必须深入递归到呈现树中，而不是立即确定已插入或删除的循环块或分支。 这通常导致必须生成更长的编辑脚本，因为差分算法获知了关于新旧结构之间关系的错误信息。

### <a name="guidance-and-conclusions"></a>指南和结论

* 如果动态生成序列号，则应用性能会受到影响。
* 该框架无法在运行时自动创建自己的序列号，因为除非在编译时捕获了必需的信息，否则这些信息不存在。
* 不要编写手动实现的冗长 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 逻辑块。 优先使用 `.razor` 文件并允许编译器处理序列号。 如果无法避免 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 手动逻辑，请将较长的代码块拆分为封装在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 调用中的较小部分。 每个区域都有自己的独立序列号空间，因此可在每个区域内从零（或任何其他任意数）重新开始。
* 如果序列号已硬编码，则差分算法仅要求序列号的值增加。 初始值和间隔不相关。 一个合理选择是使用代码行号作为序列号，或者从零开始并以 1 或 100 的间隔（或任何首选间隔）增加。 
* Blazor 使用序列号，而其他树上差分 UI 框架不使用它们。 使用序列号时，差分速度要快得多，并且 Blazor 的优势在于编译步骤可为编写 `.razor` 文件的开发人员自动处理序列号。

## <a name="perform-large-data-transfers-in-no-locblazor-server-apps"></a>在 Blazor Server 应用中执行大型数据传输

在某些方案中，必须在 JavaScript 和 Blazor 之间传输大量数据。 通常，大型数据传输在以下情况中发生：

* 浏览器文件系统 API 用于上传或下载文件。
* 需要与第三方库互操作。

Blazor Server 中存在限制，可防止传递可能导致性能问题的单个大型消息。

开发在 JavaScript 和 Blazor 之间传输数据的代码时，请考虑以下指南：

* 将数据切成小块，然后按顺序发送数据段，直到服务器收到所有数据。
* 不要在 JavaScript 和 C# 代码中分配大型对象。
* 发送或接收数据时，请勿长时间阻止主 UI 线程。
* 在进程完成或取消时释放消耗的所有内存。
* 为了安全起见，请强制执行以下附加要求：
  * 声明可以传递的最大文件或数据大小。
  * 声明从客户端到服务器的最低上传速率。
* 在服务器收到数据后，数据可以：
  * 暂时存储在内存缓冲区中，直到收集完所有数据段。
  * 立即使用。 例如，在收到每个数据段时，数据可以立即存储到数据库中或写入磁盘。

以下文件上传程序类处理与客户端的 JS 互操作。 上传程序类使用 JS 互操作：

* 轮询客户端以发送数据段。
* 在轮询超时的情况下中止事务。

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

在上面的示例中：

* `maxBase64SegmentSize` 设置为 `8192`，其由 `maxBase64SegmentSize = segmentSize * 4 / 3` 计算得出。
* 低级别 .NET Core 内存管理 API 用于在 `uploadedSegments` 中的服务器上存储内存段。
* `ReceiveFile` 方法用于处理通过 JS 互操作进行的上传：
  * 通过与 `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` 的 JS 互操作确定文件大小（以字节为单位）。
  * 在 `numberOfSegments` 中计算要接收的段数并进行存储。
  * 通过与 `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` 的 JS 互操作在 `for` 循环中请求段。 除最后一个段外，所有段都必须为 8,192 字节才能进行解码。 客户端被强制以高效方式发送数据。
  * 对于接收的每个段，在使用 <xref:System.Convert.TryFromBase64String%2A> 解码之前执行检查。
  * 上传完成后，包含数据的数据流会作为新的<xref:System.IO.Stream> (`SegmentedStream`) 返回。

分段的数据流类将段列表作为不可查找的只读 <xref:System.IO.Stream> 公开：

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

以下代码实现用于接收数据的 JavaScript 函数：

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
