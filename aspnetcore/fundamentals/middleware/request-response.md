---
title: ASP.NET Core 中的请求和响应操作
author: jkotalik
description: 了解如何在 ASP.NET Core 中读取请求正文和写入响应正文。
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: e992401da2d194b178afbe51a293d103def0f940
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238152"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="75dba-103">ASP.NET Core 中的请求和响应操作</span><span class="sxs-lookup"><span data-stu-id="75dba-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="75dba-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="75dba-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="75dba-105">本文介绍如何读取请求正文和写入响应正文。</span><span class="sxs-lookup"><span data-stu-id="75dba-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="75dba-106">写入中间件时，可能需要这些操作的代码。</span><span class="sxs-lookup"><span data-stu-id="75dba-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="75dba-107">除写入中间件外，通常不需要自定义代码，因为操作由 MVC 和 Razor Pages 处理。</span><span class="sxs-lookup"><span data-stu-id="75dba-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="75dba-108">请求正文和响应正文有两个抽象元素：<xref:System.IO.Stream> 和 <xref:System.IO.Pipelines.Pipe>。</span><span class="sxs-lookup"><span data-stu-id="75dba-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="75dba-109">对于请求读取，[HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) 是 <xref:System.IO.Stream>，`HttpRequest.BodyReader` 是 <xref:System.IO.Pipelines.PipeReader>。</span><span class="sxs-lookup"><span data-stu-id="75dba-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="75dba-110">对于响应写入，[HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) 是，`HttpResponse.BodyWriter` 是 <xref:System.IO.Pipelines.PipeWriter>。</span><span class="sxs-lookup"><span data-stu-id="75dba-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="75dba-111">建议使用管道替代流。</span><span class="sxs-lookup"><span data-stu-id="75dba-111">Pipelines are recommended over streams.</span></span> <span data-ttu-id="75dba-112">在一些简单操作中，使用流会比较简单，但管道具有性能优势，并且在大多数场景中更易于使用。</span><span class="sxs-lookup"><span data-stu-id="75dba-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="75dba-113">ASP.NET Core 开始在内部使用管道替代流。</span><span class="sxs-lookup"><span data-stu-id="75dba-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="75dba-114">示例包括：</span><span class="sxs-lookup"><span data-stu-id="75dba-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="75dba-115">流不会从框架中删除。</span><span class="sxs-lookup"><span data-stu-id="75dba-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="75dba-116">流将继续在 .NET 中使用，并且许多流类型不具有等效的管道，如 `FileStreams` 和 `ResponseCompression`。</span><span class="sxs-lookup"><span data-stu-id="75dba-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="75dba-117">流示例</span><span class="sxs-lookup"><span data-stu-id="75dba-117">Stream examples</span></span>

<span data-ttu-id="75dba-118">假设目标是要创建一个中间件，它将整个请求正文作为一个字符串列表读取，并在新行上进行拆分。</span><span class="sxs-lookup"><span data-stu-id="75dba-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="75dba-119">一个简单的流实现可能如下例所示：</span><span class="sxs-lookup"><span data-stu-id="75dba-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="75dba-120">此代码有效，但存在一些问题：</span><span class="sxs-lookup"><span data-stu-id="75dba-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="75dba-121">在追加到 `StringBuilder` 之前，示例创建另一个字符串 (`encodedString`)，该字符串会立即被丢弃。</span><span class="sxs-lookup"><span data-stu-id="75dba-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="75dba-122">此过程发生在流中的所有字节上，因此结果是为整个请求正文大小分配额外的内存。</span><span class="sxs-lookup"><span data-stu-id="75dba-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="75dba-123">该示例在新行上进行拆分之前读取整个字符串。</span><span class="sxs-lookup"><span data-stu-id="75dba-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="75dba-124">检查字节数组中的新行会更有效。</span><span class="sxs-lookup"><span data-stu-id="75dba-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="75dba-125">下面是修复上面其中一些问题的示例：</span><span class="sxs-lookup"><span data-stu-id="75dba-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="75dba-126">上面的此示例：</span><span class="sxs-lookup"><span data-stu-id="75dba-126">This preceding example:</span></span>

* <span data-ttu-id="75dba-127">不在 `StringBuilder` 中缓冲整个请求正文（除非没有任何换行符）。</span><span class="sxs-lookup"><span data-stu-id="75dba-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="75dba-128">不在字符串上调用 `Split`。</span><span class="sxs-lookup"><span data-stu-id="75dba-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="75dba-129">然而，仍然存在一些问题：</span><span class="sxs-lookup"><span data-stu-id="75dba-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="75dba-130">如果换行符稀疏，则大部分请求正文都在字符串中进行缓冲。</span><span class="sxs-lookup"><span data-stu-id="75dba-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="75dba-131">该代码会继续创建字符串 (`remainingString`) 并将它们添加到字符串缓冲区，这将导致额外的分配。</span><span class="sxs-lookup"><span data-stu-id="75dba-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="75dba-132">这些问题是可修复的，但代码逐渐变得复杂，几乎没有什么改进。</span><span class="sxs-lookup"><span data-stu-id="75dba-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="75dba-133">管道提供了一种以最低的代码复杂性解决这些问题的方法。</span><span class="sxs-lookup"><span data-stu-id="75dba-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="75dba-134">管道</span><span class="sxs-lookup"><span data-stu-id="75dba-134">Pipelines</span></span>

<span data-ttu-id="75dba-135">下面的示例显示了如何使用 `PipeReader` 处理相同的场景：</span><span class="sxs-lookup"><span data-stu-id="75dba-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="75dba-136">此示例解决了流实现中的许多问题：</span><span class="sxs-lookup"><span data-stu-id="75dba-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="75dba-137">不需要字符串缓冲区，因为 `PipeReader` 会处理未使用的字节。</span><span class="sxs-lookup"><span data-stu-id="75dba-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="75dba-138">编码后的字符串将直接添加到返回的字符串列表。</span><span class="sxs-lookup"><span data-stu-id="75dba-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="75dba-139">除了字符串使用的内存之外，无需再为创建的字符串分配内存（`ToArray()` 调用除外）。</span><span class="sxs-lookup"><span data-stu-id="75dba-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="75dba-140">适配器</span><span class="sxs-lookup"><span data-stu-id="75dba-140">Adapters</span></span>

<span data-ttu-id="75dba-141">`Body` 和 `BodyReader/BodyWriter` 属性均可用于 `HttpRequest` 和 `HttpResponse`。</span><span class="sxs-lookup"><span data-stu-id="75dba-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="75dba-142">将 `Body` 设置为另一个流时，一组新的适配器会自动使每种类型彼此适应。</span><span class="sxs-lookup"><span data-stu-id="75dba-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="75dba-143">如果将 `HttpRequest.Body` 设置为新流，则 `HttpRequest.BodyReader` 将自动设置为包装 `HttpRequest.Body` 的新 `PipeReader`。</span><span class="sxs-lookup"><span data-stu-id="75dba-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="75dba-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="75dba-144">StartAsync</span></span>

<span data-ttu-id="75dba-145">`HttpResponse.StartAsync` 用于指示标头不可修改并运行 `OnStarting` 回调。</span><span class="sxs-lookup"><span data-stu-id="75dba-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="75dba-146">使用 Kestrel 作为服务器时，在使用 `PipeReader` 之前调用 `StartAsync` 可确保 `GetMemory` 返回的内存属于 Kestrel 的内部 <xref:System.IO.Pipelines.Pipe> 而不是外部缓冲区。</span><span class="sxs-lookup"><span data-stu-id="75dba-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75dba-147">其他资源</span><span class="sxs-lookup"><span data-stu-id="75dba-147">Additional resources</span></span>

* [<span data-ttu-id="75dba-148">System.IO.Pipelines 简介</span><span class="sxs-lookup"><span data-stu-id="75dba-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
