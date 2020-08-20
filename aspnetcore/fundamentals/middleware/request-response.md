---
title: ASP.NET Core 中的请求和响应操作
author: jkotalik
description: 了解如何在 ASP.NET Core 中读取请求正文和写入响应正文。
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: ce7357ccbb52736bfb44cd8e041c68a0992bf319
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627775"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="d44e2-103">ASP.NET Core 中的请求和响应操作</span><span class="sxs-lookup"><span data-stu-id="d44e2-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="d44e2-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="d44e2-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="d44e2-105">本文介绍如何读取请求正文和写入响应正文。</span><span class="sxs-lookup"><span data-stu-id="d44e2-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="d44e2-106">写入中间件时，可能需要这些操作的代码。</span><span class="sxs-lookup"><span data-stu-id="d44e2-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="d44e2-107">除写入中间件外，通常不需要自定义代码，因为操作由 MVC 和 Razor Pages 处理。</span><span class="sxs-lookup"><span data-stu-id="d44e2-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="d44e2-108">请求正文和响应正文有两个抽象元素：<xref:System.IO.Stream> 和 <xref:System.IO.Pipelines.Pipe>。</span><span class="sxs-lookup"><span data-stu-id="d44e2-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="d44e2-109">对于请求读取，<xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> 为 <xref:System.IO.Stream>，`HttpRequest.BodyReader` 为 <xref:System.IO.Pipelines.PipeReader>。</span><span class="sxs-lookup"><span data-stu-id="d44e2-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="d44e2-110">对于响应写入，<xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> 为 <xref:System.IO.Stream>，`HttpResponse.BodyWriter` 为 <xref:System.IO.Pipelines.PipeWriter>。</span><span class="sxs-lookup"><span data-stu-id="d44e2-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="d44e2-111">建议使用[管道](/dotnet/standard/io/pipelines)替代流。</span><span class="sxs-lookup"><span data-stu-id="d44e2-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="d44e2-112">在一些简单操作中，使用流会比较简单，但管道具有性能优势，并且在大多数场景中更易于使用。</span><span class="sxs-lookup"><span data-stu-id="d44e2-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="d44e2-113">ASP.NET Core 开始在内部使用管道替代流。</span><span class="sxs-lookup"><span data-stu-id="d44e2-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="d44e2-114">示例包括：</span><span class="sxs-lookup"><span data-stu-id="d44e2-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="d44e2-115">流不会从框架中删除。</span><span class="sxs-lookup"><span data-stu-id="d44e2-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="d44e2-116">流将继续在 .NET 中使用，并且许多流类型不具有等效的管道，如 `FileStreams` 和 `ResponseCompression`。</span><span class="sxs-lookup"><span data-stu-id="d44e2-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="d44e2-117">流示例</span><span class="sxs-lookup"><span data-stu-id="d44e2-117">Stream examples</span></span>

<span data-ttu-id="d44e2-118">假设目标是要创建一个中间件，它将整个请求正文作为一个字符串列表读取，并在新行上进行拆分。</span><span class="sxs-lookup"><span data-stu-id="d44e2-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="d44e2-119">一个简单的流实现可能如下例所示：</span><span class="sxs-lookup"><span data-stu-id="d44e2-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="d44e2-120">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="d44e2-120">The following code:</span></span>
> * <span data-ttu-id="d44e2-121">用于演示不使用管道读取请求正文的问题。</span><span class="sxs-lookup"><span data-stu-id="d44e2-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="d44e2-122">不应在生产应用中使用。</span><span class="sxs-lookup"><span data-stu-id="d44e2-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="d44e2-123">此代码有效，但存在一些问题：</span><span class="sxs-lookup"><span data-stu-id="d44e2-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="d44e2-124">在追加到 `StringBuilder` 之前，示例创建另一个字符串 (`encodedString`)，该字符串会立即被丢弃。</span><span class="sxs-lookup"><span data-stu-id="d44e2-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="d44e2-125">此过程发生在流中的所有字节上，因此结果是为整个请求正文大小分配额外的内存。</span><span class="sxs-lookup"><span data-stu-id="d44e2-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="d44e2-126">该示例在新行上进行拆分之前读取整个字符串。</span><span class="sxs-lookup"><span data-stu-id="d44e2-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="d44e2-127">检查字节数组中的新行会更有效。</span><span class="sxs-lookup"><span data-stu-id="d44e2-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="d44e2-128">下面是修复上面其中一些问题的示例：</span><span class="sxs-lookup"><span data-stu-id="d44e2-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="d44e2-129">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="d44e2-129">The following code:</span></span>
> * <span data-ttu-id="d44e2-130">用于演示前面代码中的一些问题的解决方案，而不能解决所有问题。</span><span class="sxs-lookup"><span data-stu-id="d44e2-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="d44e2-131">不应在生产应用中使用。</span><span class="sxs-lookup"><span data-stu-id="d44e2-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="d44e2-132">上面的此示例：</span><span class="sxs-lookup"><span data-stu-id="d44e2-132">This preceding example:</span></span>

* <span data-ttu-id="d44e2-133">不在 `StringBuilder` 中缓冲整个请求正文（除非没有任何换行符）。</span><span class="sxs-lookup"><span data-stu-id="d44e2-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="d44e2-134">不在字符串上调用 `Split`。</span><span class="sxs-lookup"><span data-stu-id="d44e2-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="d44e2-135">然而，仍然存在一些问题：</span><span class="sxs-lookup"><span data-stu-id="d44e2-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="d44e2-136">如果换行符稀疏，则大部分请求正文都在字符串中进行缓冲。</span><span class="sxs-lookup"><span data-stu-id="d44e2-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="d44e2-137">该代码会继续创建字符串 (`remainingString`) 并将它们添加到字符串缓冲区，这将导致额外的分配。</span><span class="sxs-lookup"><span data-stu-id="d44e2-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="d44e2-138">这些问题是可修复的，但代码逐渐变得复杂，几乎没有什么改进。</span><span class="sxs-lookup"><span data-stu-id="d44e2-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="d44e2-139">管道提供了一种以最低的代码复杂性解决这些问题的方法。</span><span class="sxs-lookup"><span data-stu-id="d44e2-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="d44e2-140">管道</span><span class="sxs-lookup"><span data-stu-id="d44e2-140">Pipelines</span></span>

<span data-ttu-id="d44e2-141">下面的示例显示了如何使用 [PipeReader](/dotnet/standard/io/pipelines#pipe) 处理相同的场景：</span><span class="sxs-lookup"><span data-stu-id="d44e2-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="d44e2-142">此示例解决了流实现中的许多问题：</span><span class="sxs-lookup"><span data-stu-id="d44e2-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="d44e2-143">不需要字符串缓冲区，因为 `PipeReader` 会处理未使用的字节。</span><span class="sxs-lookup"><span data-stu-id="d44e2-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="d44e2-144">编码后的字符串将直接添加到返回的字符串列表。</span><span class="sxs-lookup"><span data-stu-id="d44e2-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="d44e2-145">除了 `ToArray` 调用以及字符串使用的内存，创建字符串时无需分配。</span><span class="sxs-lookup"><span data-stu-id="d44e2-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="d44e2-146">适配器</span><span class="sxs-lookup"><span data-stu-id="d44e2-146">Adapters</span></span>

<span data-ttu-id="d44e2-147">`Body`、`BodyReader` 和 `BodyWriter` 属性可用于 `HttpRequest` 和 `HttpResponse`。</span><span class="sxs-lookup"><span data-stu-id="d44e2-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="d44e2-148">将 `Body` 设置为另一个流时，一组新的适配器会自动使每种类型彼此适应。</span><span class="sxs-lookup"><span data-stu-id="d44e2-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="d44e2-149">如果将 `HttpRequest.Body` 设置为新流，则 `HttpRequest.BodyReader` 将自动设置为包装 `HttpRequest.Body` 的新 `PipeReader`。</span><span class="sxs-lookup"><span data-stu-id="d44e2-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="d44e2-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d44e2-150">StartAsync</span></span>

<span data-ttu-id="d44e2-151">`HttpResponse.StartAsync` 用于指示标头不可修改并运行 `OnStarting` 回调。</span><span class="sxs-lookup"><span data-stu-id="d44e2-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="d44e2-152">使用 Kestrel 作为服务器时，在使用 `PipeReader` 之前调用 `StartAsync` 可确保 `GetMemory` 返回的内存属于 Kestrel 的内部 <xref:System.IO.Pipelines.Pipe> 而不是外部缓冲区。</span><span class="sxs-lookup"><span data-stu-id="d44e2-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d44e2-153">其他资源</span><span class="sxs-lookup"><span data-stu-id="d44e2-153">Additional resources</span></span>

* [<span data-ttu-id="d44e2-154">.NET 中的 System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="d44e2-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
