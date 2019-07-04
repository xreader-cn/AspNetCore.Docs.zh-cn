---
title: ASP.NET Core 中的请求和响应操作
author: jkotalik
description: 了解如何在 ASP.NET Core 中读取请求正文和写入响应正文。
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: 0c321dad256e239b61907980c09d2c088c1407ff
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538575"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="21d1e-103">ASP.NET Core 中的请求和响应操作</span><span class="sxs-lookup"><span data-stu-id="21d1e-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="21d1e-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="21d1e-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="21d1e-105">本文介绍如何读取请求正文和写入响应正文。</span><span class="sxs-lookup"><span data-stu-id="21d1e-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="21d1e-106">在编写中间件时，可能需要为这些操作编写代码。</span><span class="sxs-lookup"><span data-stu-id="21d1e-106">You might need to write code for these operations when you're writing middleware.</span></span> <span data-ttu-id="21d1e-107">除此之外，通常不需要编写此代码，因为操作由 MVC 和 Razor Pages 处理。</span><span class="sxs-lookup"><span data-stu-id="21d1e-107">Otherwise, you typically don't have to write this code because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="21d1e-108">在 ASP.NET Core 3.0 中，请求正文和响应正文有两个抽象元素：<xref:System.IO.Stream> 和 <xref:System.IO.Pipelines.Pipe>。</span><span class="sxs-lookup"><span data-stu-id="21d1e-108">In ASP.NET Core 3.0, there are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="21d1e-109">对于请求读取，[HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) 是 <xref:System.IO.Stream>，`HttpRequest.BodyPipe` 是 <xref:System.IO.Pipelines.PipeReader>。</span><span class="sxs-lookup"><span data-stu-id="21d1e-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyPipe` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="21d1e-110">对于响应写入，[HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) 是，`HttpResponse.BodyPipe` 是 <xref:System.IO.Pipelines.PipeWriter>。</span><span class="sxs-lookup"><span data-stu-id="21d1e-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a `HttpResponse.BodyPipe` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="21d1e-111">建议使用管道替代流。</span><span class="sxs-lookup"><span data-stu-id="21d1e-111">We recommend pipelines over streams.</span></span> <span data-ttu-id="21d1e-112">在一些简单操作中，使用流会比较简单，但管道具有性能优势，并且在大多数场景中更易于使用。</span><span class="sxs-lookup"><span data-stu-id="21d1e-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="21d1e-113">在 3.0 中，ASP.NET Core 开始在内部使用管道替代流。</span><span class="sxs-lookup"><span data-stu-id="21d1e-113">In 3.0, ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="21d1e-114">示例包括：</span><span class="sxs-lookup"><span data-stu-id="21d1e-114">Examples include:</span></span>

- `FormReader`
- `TextReader`
- `TexWriter`
- `HttpResponse.WriteAsync`

<span data-ttu-id="21d1e-115">流不会消失。</span><span class="sxs-lookup"><span data-stu-id="21d1e-115">Streams aren't going away.</span></span> <span data-ttu-id="21d1e-116">它们将继续在 .NET 中使用，并且许多流类型不具有等效的管道，如 `FileStreams` 和 `ResponseCompression`。</span><span class="sxs-lookup"><span data-stu-id="21d1e-116">They continue to be used throughout .NET, and many stream types don't have pipe equivalents, like `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="21d1e-117">流示例</span><span class="sxs-lookup"><span data-stu-id="21d1e-117">Stream examples</span></span>

<span data-ttu-id="21d1e-118">假设你想创建一个中间件，它将整个请求正文作为一个字符串列表读取，并在新行上进行拆分。</span><span class="sxs-lookup"><span data-stu-id="21d1e-118">Suppose you want to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="21d1e-119">一个简单的流实现可能如下例所示：</span><span class="sxs-lookup"><span data-stu-id="21d1e-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="21d1e-120">此代码有效，但存在一些问题：</span><span class="sxs-lookup"><span data-stu-id="21d1e-120">This code works, but there are some issues:</span></span>

- <span data-ttu-id="21d1e-121">在追加到 `StringBuilder` 之前，示例创建另一个字符串 (`encodedString`)，该字符串会立即被丢弃。</span><span class="sxs-lookup"><span data-stu-id="21d1e-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="21d1e-122">此过程发生在流中的所有字节上，因此结果是为整个请求正文大小分配额外的内存。</span><span class="sxs-lookup"><span data-stu-id="21d1e-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
- <span data-ttu-id="21d1e-123">该示例在新行上进行拆分之前读取整个字符串。</span><span class="sxs-lookup"><span data-stu-id="21d1e-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="21d1e-124">检查字节数组中的新行会更有效。</span><span class="sxs-lookup"><span data-stu-id="21d1e-124">It would be more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="21d1e-125">下面是修复其中一些问题的示例：</span><span class="sxs-lookup"><span data-stu-id="21d1e-125">Here's an example that fixes some of these issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="21d1e-126">本示例：</span><span class="sxs-lookup"><span data-stu-id="21d1e-126">This example:</span></span>

- <span data-ttu-id="21d1e-127">不在 `StringBuilder` 中缓冲整个请求正文（除非没有任何换行符）。</span><span class="sxs-lookup"><span data-stu-id="21d1e-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
- <span data-ttu-id="21d1e-128">不在字符串上调用 `Split`。</span><span class="sxs-lookup"><span data-stu-id="21d1e-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="21d1e-129">然而，仍然存在一些问题：</span><span class="sxs-lookup"><span data-stu-id="21d1e-129">However, there are still are a few issues:</span></span>

- <span data-ttu-id="21d1e-130">如果换行符稀疏，则大部分请求正文都在字符串中进行缓冲。</span><span class="sxs-lookup"><span data-stu-id="21d1e-130">If newline characters are sparse, much of the request body is buffered in the string .</span></span>
- <span data-ttu-id="21d1e-131">它仍然创建字符串 (`remainingString`) 并将它们添加到字符串缓冲区，这将导致额外的分配。</span><span class="sxs-lookup"><span data-stu-id="21d1e-131">It still creates strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="21d1e-132">这些问题是可修复的，但代码变得越来越复杂，几乎没有什么改进。</span><span class="sxs-lookup"><span data-stu-id="21d1e-132">These issues are fixable, but the code is becoming more and more complicated with little improvement.</span></span> <span data-ttu-id="21d1e-133">管道提供了一种以最低的代码复杂性解决这些问题的方法。</span><span class="sxs-lookup"><span data-stu-id="21d1e-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="21d1e-134">管道</span><span class="sxs-lookup"><span data-stu-id="21d1e-134">Pipelines</span></span>

<span data-ttu-id="21d1e-135">下面的示例显示了如何使用 `PipeReader` 处理相同的场景：</span><span class="sxs-lookup"><span data-stu-id="21d1e-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="21d1e-136">此示例解决了流实现中的许多问题：</span><span class="sxs-lookup"><span data-stu-id="21d1e-136">This example fixes many issues that the streams implementations had:</span></span>

- <span data-ttu-id="21d1e-137">不需要字符串缓冲区，因为 `PipeReader` 会处理未使用的字节。</span><span class="sxs-lookup"><span data-stu-id="21d1e-137">There is no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
- <span data-ttu-id="21d1e-138">编码后的字符串将直接添加到返回的字符串列表。</span><span class="sxs-lookup"><span data-stu-id="21d1e-138">Encoded strings are directly added to the list of returned strings.</span></span>
- <span data-ttu-id="21d1e-139">除了字符串使用的内存之外，无需再为创建的字符串分配内存（`ToArray()` 调用除外）。</span><span class="sxs-lookup"><span data-stu-id="21d1e-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="21d1e-140">适配器</span><span class="sxs-lookup"><span data-stu-id="21d1e-140">Adapters</span></span>

<span data-ttu-id="21d1e-141">既然 `Body` 和 `BodyPipe` 属性都可用于 `HttpRequest` 和 `HttpResponse`，那么，如果将 `Body` 设置为不同的流，会是什么情况？</span><span class="sxs-lookup"><span data-stu-id="21d1e-141">Now that both `Body` and `BodyPipe` properties are available for `HttpRequest` and `HttpResponse`, what happens when you set `Body` to a different stream?</span></span> <span data-ttu-id="21d1e-142">在 3.0 中，一组新的适配器会自动使每种类型彼此适应。</span><span class="sxs-lookup"><span data-stu-id="21d1e-142">In 3.0, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="21d1e-143">例如，如果将 `HttpRequest.Body` 设置为新流，则 `HttpRequest.BodyPipe` 将自动设置为包装 `HttpRequest.Body` 的新 `PipeReader`。</span><span class="sxs-lookup"><span data-stu-id="21d1e-143">For example, if you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyPipe` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span> <span data-ttu-id="21d1e-144">相同的行为适用于设置 `BodyPipe` 属性。</span><span class="sxs-lookup"><span data-stu-id="21d1e-144">The same behavior applies to setting the `BodyPipe` property.</span></span> <span data-ttu-id="21d1e-145">如果 `HttpResponse.BodyPipe` 设置为新 `PipeWriter`，则 `HttpResponse.Body` 自动设置为包装 `HttpResponse.BodyPipe` 的新流。</span><span class="sxs-lookup"><span data-stu-id="21d1e-145">If `HttpResponse.BodyPipe` is set to a new `PipeWriter`, the `HttpResponse.Body` is automatically set to a new stream that wraps `HttpResponse.BodyPipe`.</span></span>

## <a name="startasync"></a><span data-ttu-id="21d1e-146">StartAsync</span><span class="sxs-lookup"><span data-stu-id="21d1e-146">StartAsync</span></span>

<span data-ttu-id="21d1e-147">`HttpResponse.StartAsync` 是 3.0 中的新增内容。</span><span class="sxs-lookup"><span data-stu-id="21d1e-147">`HttpResponse.StartAsync` is new in 3.0.</span></span> <span data-ttu-id="21d1e-148">它用于指示标头不可修改并运行 `OnStarting` 回调。</span><span class="sxs-lookup"><span data-stu-id="21d1e-148">It is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="21d1e-149">在 3.0-preview3 中，必须在使用 `HttpRequest.BodyPipe` 之前调用 `StartAsync`，在将来的版本中也建议这样做。</span><span class="sxs-lookup"><span data-stu-id="21d1e-149">In 3.0-preview3, you have to call `StartAsync` before using `HttpRequest.BodyPipe`, and in future releases, it will be a recommendation.</span></span> <span data-ttu-id="21d1e-150">使用 Kestrel 作为服务器时，在使用 `PipeReader` 之前调用 StartAsync 可确保 `GetMemory` 返回的内存属于 Kestrel 的内部 <xref:System.IO.Pipelines.Pipe> 而不是外部缓冲区。</span><span class="sxs-lookup"><span data-stu-id="21d1e-150">When using Kestrel as a server, calling StartAsync before using the `PipeReader` guarantees that memory returned by `GetMemory` will belong to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21d1e-151">其他资源</span><span class="sxs-lookup"><span data-stu-id="21d1e-151">Additional resources</span></span>

- [<span data-ttu-id="21d1e-152">System.IO.Pipelines 简介</span><span class="sxs-lookup"><span data-stu-id="21d1e-152">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
- <xref:fundamentals/middleware/write>