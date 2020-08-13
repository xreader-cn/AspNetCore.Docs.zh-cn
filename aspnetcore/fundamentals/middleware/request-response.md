---
title: ASP.NET Core 中的请求和响应操作
author: jkotalik
description: 了解如何在 ASP.NET Core 中读取请求正文和写入响应正文。
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
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
uid: fundamentals/middleware/request-response
ms.openlocfilehash: da863ac5ecf649adffe8a3d13838be2ac1f748c2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016955"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>ASP.NET Core 中的请求和响应操作

作者：[Justin Kotalik](https://github.com/jkotalik)

本文介绍如何读取请求正文和写入响应正文。 写入中间件时，可能需要这些操作的代码。 除写入中间件外，通常不需要自定义代码，因为操作由 MVC 和 Razor Pages 处理。

请求正文和响应正文有两个抽象元素：<xref:System.IO.Stream> 和 <xref:System.IO.Pipelines.Pipe>。 对于请求读取，<xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> 为 <xref:System.IO.Stream>，`HttpRequest.BodyReader` 为 <xref:System.IO.Pipelines.PipeReader>。 对于响应写入，<xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> 为 <xref:System.IO.Stream>，`HttpResponse.BodyWriter` 为 <xref:System.IO.Pipelines.PipeWriter>。

建议使用[管道](/dotnet/standard/io/pipelines)替代流。 在一些简单操作中，使用流会比较简单，但管道具有性能优势，并且在大多数场景中更易于使用。 ASP.NET Core 开始在内部使用管道替代流。 示例包括：

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

流不会从框架中删除。 流将继续在 .NET 中使用，并且许多流类型不具有等效的管道，如 `FileStreams` 和 `ResponseCompression`。

## <a name="stream-examples"></a>流示例

假设目标是要创建一个中间件，它将整个请求正文作为一个字符串列表读取，并在新行上进行拆分。 一个简单的流实现可能如下例所示：

> [!WARNING]
> 下面的代码：
> * 用于演示不使用管道读取请求正文的问题。
> * 不应在生产应用中使用。

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

此代码有效，但存在一些问题：

* 在追加到 `StringBuilder` 之前，示例创建另一个字符串 (`encodedString`)，该字符串会立即被丢弃。 此过程发生在流中的所有字节上，因此结果是为整个请求正文大小分配额外的内存。
* 该示例在新行上进行拆分之前读取整个字符串。 检查字节数组中的新行会更有效。

下面是修复上面其中一些问题的示例：

> [!WARNING]
> 下面的代码：
> * 用于演示前面代码中的一些问题的解决方案，而不能解决所有问题。
> * 不应在生产应用中使用。

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

上面的此示例：

* 不在 `StringBuilder` 中缓冲整个请求正文（除非没有任何换行符）。
* 不在字符串上调用 `Split`。

然而，仍然存在一些问题：

* 如果换行符稀疏，则大部分请求正文都在字符串中进行缓冲。
* 该代码会继续创建字符串 (`remainingString`) 并将它们添加到字符串缓冲区，这将导致额外的分配。

这些问题是可修复的，但代码逐渐变得复杂，几乎没有什么改进。 管道提供了一种以最低的代码复杂性解决这些问题的方法。

## <a name="pipelines"></a>管道

下面的示例显示了如何使用 [PipeReader](/dotnet/standard/io/pipelines#pipe) 处理相同的场景：

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

此示例解决了流实现中的许多问题：

* 不需要字符串缓冲区，因为 `PipeReader` 会处理未使用的字节。
* 编码后的字符串将直接添加到返回的字符串列表。
* 除了 `ToArray` 调用以及字符串使用的内存，创建字符串时无需分配。

## <a name="adapters"></a>适配器

`Body`、`BodyReader` 和 `BodyWriter` 属性可用于 `HttpRequest` 和 `HttpResponse`。 将 `Body` 设置为另一个流时，一组新的适配器会自动使每种类型彼此适应。 如果将 `HttpRequest.Body` 设置为新流，则 `HttpRequest.BodyReader` 将自动设置为包装 `HttpRequest.Body` 的新 `PipeReader`。

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` 用于指示标头不可修改并运行 `OnStarting` 回调。 使用 Kestrel 作为服务器时，在使用 `PipeReader` 之前调用 `StartAsync` 可确保 `GetMemory` 返回的内存属于 Kestrel 的内部 <xref:System.IO.Pipelines.Pipe> 而不是外部缓冲区。

## <a name="additional-resources"></a>其他资源

* [.NET 中的 System.IO.Pipelines](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
