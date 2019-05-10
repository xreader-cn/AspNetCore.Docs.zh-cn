---
title: ASP.NET Core 中的请求和响应操作
author: jkotalik
description: 了解如何在 ASP.NET Core 中读取请求正文和写入响应正文。
monikerRange: '>= aspnetcore-3.0'
ms.author: jkotalik
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b6e3cd4b79e0c062b271c65cd5ecbdb4ef80c3a1
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085523"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>ASP.NET Core 中的请求和响应操作

作者：[Justin Kotalik](https://github.com/jkotalik)

本文介绍如何读取请求正文和写入响应正文。 在编写中间件时，可能需要为这些操作编写代码。 除此之外，通常不需要编写此代码，因为操作由 MVC 和 Razor Pages 处理。

在 ASP.NET Core 3.0 中，请求正文和响应正文有两个抽象元素：<xref:System.IO.Stream> 和 <xref:System.IO.Pipelines.Pipe>。 对于请求读取，[HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) 是 <xref:System.IO.Stream>，`HttpRequest.BodyPipe` 是 <xref:System.IO.Pipelines.PipeReader>。 对于响应写入，[HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) 是，`HttpResponse.BodyPipe` 是 <xref:System.IO.Pipelines.PipeWriter>。

建议使用管道替代流。 在一些简单操作中，使用流会比较简单，但管道具有性能优势，并且在大多数场景中更易于使用。 在 3.0 中，ASP.NET Core 开始在内部使用管道替代流。 示例包括：

- `FormReader`
- `TextReader`
- `TexWriter`
- `HttpResponse.WriteAsync`

流不会消失。 它们将继续在 .NET 中使用，并且许多流类型不具有等效的管道，如 `FileStreams` 和 `ResponseCompression`。

## <a name="stream-examples"></a>流示例

假设你想创建一个中间件，它将整个请求正文作为一个字符串列表读取，并在新行上进行拆分。 一个简单的流实现可能如下例所示：

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

此代码有效，但存在一些问题：

- 在追加到 `StringBuilder` 之前，示例创建另一个字符串 (`encodedString`)，该字符串会立即被丢弃。 此过程发生在流中的所有字节上，因此结果是为整个请求正文大小分配额外的内存。
- 该示例在新行上进行拆分之前读取整个字符串。 检查字节数组中的新行会更有效。

下面是修复其中一些问题的示例：

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

本示例：

- 不在 `StringBuilder` 中缓冲整个请求正文（除非没有任何换行符）。
- 不在字符串上调用 `Split`。

然而，仍然存在一些问题：

- 如果换行符稀疏，则大部分请求正文都在字符串中进行缓冲。
- 它仍然创建字符串 (`remainingString`) 并将它们添加到字符串缓冲区，这将导致额外的分配。

这些问题是可修复的，但代码变得越来越复杂，几乎没有什么改进。 管道提供了一种以最低的代码复杂性解决这些问题的方法。

## <a name="pipelines"></a>管道

下面的示例显示了如何使用 `PipeReader` 处理相同的场景：

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

此示例解决了流实现中的许多问题：

- 不需要字符串缓冲区，因为 `PipeReader` 会处理未使用的字节。
- 编码后的字符串将直接添加到返回的字符串列表。
- 除了字符串使用的内存之外，无需再为创建的字符串分配内存（`ToArray()` 调用除外）。

## <a name="adapters"></a>适配器

既然 `Body` 和 `BodyPipe` 属性都可用于 `HttpRequest` 和 `HttpResponse`，那么，如果将 `Body` 设置为不同的流，会是什么情况？ 在 3.0 中，一组新的适配器会自动使每种类型彼此适应。 例如，如果将 `HttpRequest.Body` 设置为新流，则 `HttpRequest.BodyPipe` 将自动设置为包装 `HttpRequest.Body` 的新 `PipeReader`。 相同的行为适用于设置 `BodyPipe` 属性。 如果 `HttpResponse.BodyPipe` 设置为新 `PipeWriter`，则 `HttpResponse.Body` 自动设置为包装 `HttpResponse.BodyPipe` 的新流。

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` 是 3.0 中的新增内容。 它用于指示标头不可修改并运行 `OnStarting` 回调。 在 3.0-preview3 中，必须在使用 `HttpRequest.BodyPipe` 之前调用 `StartAsync`，在将来的版本中也建议这样做。 使用 Kestrel 作为服务器时，在使用 `PipeReader` 之前调用 StartAsync 可确保 `GetMemory` 返回的内存属于 Kestrel 的内部 <xref:System.IO.Pipelines.Pipe> 而不是外部缓冲区。

## <a name="additional-resources"></a>其他资源

- [System.IO.Pipelines 简介](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
- <xref:fundamentals/middleware/write>