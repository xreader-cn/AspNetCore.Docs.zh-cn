---
title: ASP.NET Core 中的响应压缩
author: rick-anderson
description: 了解响应压缩以及如何在 ASP.NET Core 应用中使用响应压缩中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/response-compression
ms.openlocfilehash: 1dd931d0ee654b888814df8a0d0675d32b5c3a20
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020959"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="6aeca-103">ASP.NET Core 中的响应压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-103">Response compression in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6aeca-104">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="6aeca-104">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="6aeca-105">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="6aeca-105">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="6aeca-106">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-106">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="6aeca-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6aeca-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="6aeca-108">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="6aeca-108">When to use Response Compression Middleware</span></span>

<span data-ttu-id="6aeca-109">在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="6aeca-109">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="6aeca-110">中间件的性能与服务器模块的性能可能不一致。</span><span class="sxs-lookup"><span data-stu-id="6aeca-110">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="6aeca-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) Server 和[Kestrel](xref:fundamentals/servers/kestrel) server 目前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="6aeca-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="6aeca-112">使用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="6aeca-112">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="6aeca-113">无法使用以下基于服务器的压缩技术：</span><span class="sxs-lookup"><span data-stu-id="6aeca-113">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="6aeca-114">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="6aeca-114">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="6aeca-115">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="6aeca-115">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="6aeca-116">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-116">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="6aeca-117">直接在上托管：</span><span class="sxs-lookup"><span data-stu-id="6aeca-117">Hosting directly on:</span></span>
  * <span data-ttu-id="6aeca-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (以前称为 WebListener) </span><span class="sxs-lookup"><span data-stu-id="6aeca-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="6aeca-119">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="6aeca-119">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="6aeca-120">响应压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-120">Response compression</span></span>

<span data-ttu-id="6aeca-121">通常，任何未进行本机压缩的响应都可以从响应压缩中获益。</span><span class="sxs-lookup"><span data-stu-id="6aeca-121">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="6aeca-122">不以本机方式压缩的响应通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="6aeca-122">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="6aeca-123">不应压缩本机压缩的资产，例如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="6aeca-123">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="6aeca-124">如果尝试进一步压缩本机压缩的响应，则可能会相形见绌处理压缩所需的时间，从而减少大小和传输时间的任何小部分。</span><span class="sxs-lookup"><span data-stu-id="6aeca-124">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="6aeca-125">请勿压缩小于大约150-1000 字节的文件 (具体取决于文件的内容和压缩) 的效率。</span><span class="sxs-lookup"><span data-stu-id="6aeca-125">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="6aeca-126">压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="6aeca-126">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="6aeca-127">如果客户端可以处理压缩的内容，则客户端必须通过请求发送标头来通知服务器的功能 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-127">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="6aeca-128">当服务器发送压缩的内容时，它必须在标头中包含有关 `Content-Encoding` 如何对压缩的响应进行编码的信息。</span><span class="sxs-lookup"><span data-stu-id="6aeca-128">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="6aeca-129">下表显示了中间件支持的内容编码称号。</span><span class="sxs-lookup"><span data-stu-id="6aeca-129">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="6aeca-130">`Accept-Encoding`标头值</span><span class="sxs-lookup"><span data-stu-id="6aeca-130">`Accept-Encoding` header values</span></span> | <span data-ttu-id="6aeca-131">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="6aeca-131">Middleware Supported</span></span> | <span data-ttu-id="6aeca-132">描述</span><span class="sxs-lookup"><span data-stu-id="6aeca-132">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="6aeca-133">是（默认）</span><span class="sxs-lookup"><span data-stu-id="6aeca-133">Yes (default)</span></span>        | [<span data-ttu-id="6aeca-134">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-134">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="6aeca-135">否</span><span class="sxs-lookup"><span data-stu-id="6aeca-135">No</span></span>                   | [<span data-ttu-id="6aeca-136">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-136">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="6aeca-137">否</span><span class="sxs-lookup"><span data-stu-id="6aeca-137">No</span></span>                   | [<span data-ttu-id="6aeca-138">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="6aeca-138">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="6aeca-139">是</span><span class="sxs-lookup"><span data-stu-id="6aeca-139">Yes</span></span>                  | [<span data-ttu-id="6aeca-140">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-140">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="6aeca-141">是</span><span class="sxs-lookup"><span data-stu-id="6aeca-141">Yes</span></span>                  | <span data-ttu-id="6aeca-142">"无编码" 标识符：不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="6aeca-142">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="6aeca-143">否</span><span class="sxs-lookup"><span data-stu-id="6aeca-143">No</span></span>                   | [<span data-ttu-id="6aeca-144">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-144">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="6aeca-145">是</span><span class="sxs-lookup"><span data-stu-id="6aeca-145">Yes</span></span>                  | <span data-ttu-id="6aeca-146">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="6aeca-146">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="6aeca-147">有关详细信息，请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-147">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="6aeca-148">中间件允许您为自定义标头值添加其他压缩提供程序 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-148">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="6aeca-149">有关详细信息，请参阅下面的[自定义提供程序](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-149">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="6aeca-150">中间件能够对质量值 (qvalue， `q` 客户端发送时) 权重来确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="6aeca-150">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="6aeca-151">有关详细信息，请参阅[RFC 7231：接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-151">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="6aeca-152">压缩算法会在压缩速度与压缩效率之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="6aeca-152">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="6aeca-153">此上下文的*有效性*是指压缩后的输出大小。</span><span class="sxs-lookup"><span data-stu-id="6aeca-153">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="6aeca-154">最小大小是通过*最佳*压缩来实现的。</span><span class="sxs-lookup"><span data-stu-id="6aeca-154">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="6aeca-155">下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-155">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="6aeca-156">标头</span><span class="sxs-lookup"><span data-stu-id="6aeca-156">Header</span></span>             | <span data-ttu-id="6aeca-157">角色</span><span class="sxs-lookup"><span data-stu-id="6aeca-157">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="6aeca-158">从客户端发送到服务器，以指示客户端可接受的内容编码方案。</span><span class="sxs-lookup"><span data-stu-id="6aeca-158">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="6aeca-159">从服务器发送到客户端，以指示有效负载中内容的编码。</span><span class="sxs-lookup"><span data-stu-id="6aeca-159">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="6aeca-160">进行压缩时，将 `Content-Length` 删除该标头，因为在对响应进行压缩时，正文内容会发生更改。</span><span class="sxs-lookup"><span data-stu-id="6aeca-160">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="6aeca-161">进行压缩时，将 `Content-MD5` 删除该标头，因为正文内容已更改且哈希不再有效。</span><span class="sxs-lookup"><span data-stu-id="6aeca-161">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="6aeca-162">指定内容的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="6aeca-162">Specifies the MIME type of the content.</span></span> <span data-ttu-id="6aeca-163">每个响应都应指定其 `Content-Type` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-163">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="6aeca-164">中间件会检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-164">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="6aeca-165">中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types)，但你可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="6aeca-165">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="6aeca-166">当由服务器发送的值 `Accept-Encoding` 为 "客户端" 和 "代理服务器" 时， `Vary` 标头向客户端或代理发送应缓存 (根据 `Accept-Encoding` 请求标头的值改变) 响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-166">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="6aeca-167">将内容与标头一起返回的结果 `Vary: Accept-Encoding` 是：压缩的和未压缩的响应都单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="6aeca-167">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="6aeca-168">浏览用于[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。</span><span class="sxs-lookup"><span data-stu-id="6aeca-168">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="6aeca-169">该示例演示：</span><span class="sxs-lookup"><span data-stu-id="6aeca-169">The sample illustrates:</span></span>

* <span data-ttu-id="6aeca-170">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-170">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="6aeca-171">如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-171">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="6aeca-172">Package</span><span class="sxs-lookup"><span data-stu-id="6aeca-172">Package</span></span>

<span data-ttu-id="6aeca-173">响应压缩中间件由[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包提供，后者隐式包含在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-173">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="6aeca-174">配置</span><span class="sxs-lookup"><span data-stu-id="6aeca-174">Configuration</span></span>

<span data-ttu-id="6aeca-175">下面的代码演示如何为默认 MIME 类型和压缩提供程序 ([Brotli](#brotli-compression-provider)和[Gzip](#gzip-compression-provider)) 启用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="6aeca-175">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="6aeca-176">说明：</span><span class="sxs-lookup"><span data-stu-id="6aeca-176">Notes:</span></span>

* <span data-ttu-id="6aeca-177">`app.UseResponseCompression`必须在压缩响应的任何中间件前调用。</span><span class="sxs-lookup"><span data-stu-id="6aeca-177">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="6aeca-178">有关详细信息，请参阅 <xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="6aeca-178">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="6aeca-179">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具设置 `Accept-Encoding` 请求标头，并检查响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="6aeca-179">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="6aeca-180">在没有标头的情况下向示例应用程序提交请求 `Accept-Encoding` ，并观察响应是否未压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-180">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="6aeca-181">`Content-Encoding`响应中 `Vary` 不存在和标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-181">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口显示请求的结果，无需接受编码标头。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="6aeca-184">将请求提交到带有 `Accept-Encoding: br` 标头 (Brotli 压缩) 的示例应用，并观察响应是否已压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-184">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="6aeca-185">`Content-Encoding`和 `Vary` 标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-185">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值为 br 的请求的结果。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="6aeca-189">提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-189">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="6aeca-190">Brotli 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-190">Brotli Compression Provider</span></span>

<span data-ttu-id="6aeca-191">使用压缩 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 压缩数据格式](https://tools.ietf.org/html/rfc7932)的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-191">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="6aeca-192">如果没有将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="6aeca-192">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="6aeca-193">默认情况下，Brotli 压缩提供程序会随[Gzip 压缩提供程序](#gzip-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-193">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="6aeca-194">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-194">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="6aeca-195">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="6aeca-195">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="6aeca-196">在显式添加任何压缩提供程序时，必须添加 Brotli 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="6aeca-196">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6aeca-197">设置的压缩级别 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-197">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="6aeca-198">Brotli 压缩提供程序默认为最快的压缩级别 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) ，这可能不会产生最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-198">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="6aeca-199">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-199">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="6aeca-200">Compression Level</span><span class="sxs-lookup"><span data-stu-id="6aeca-200">Compression Level</span></span> | <span data-ttu-id="6aeca-201">描述</span><span class="sxs-lookup"><span data-stu-id="6aeca-201">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="6aeca-202">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="6aeca-202">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-203">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-203">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="6aeca-204">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="6aeca-204">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-205">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-205">No compression should be performed.</span></span> |
| [<span data-ttu-id="6aeca-206">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="6aeca-206">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-207">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-207">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a><span data-ttu-id="6aeca-208">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-208">Gzip Compression Provider</span></span>

<span data-ttu-id="6aeca-209">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-209">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="6aeca-210">如果没有将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="6aeca-210">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="6aeca-211">默认情况下，Gzip 压缩提供程序随[Brotli 压缩提供程序](#brotli-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-211">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="6aeca-212">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-212">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="6aeca-213">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="6aeca-213">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="6aeca-214">在显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="6aeca-214">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="6aeca-215">设置的压缩级别 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-215">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="6aeca-216">Gzip 压缩提供程序默认为最快的压缩级别 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) ，这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-216">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="6aeca-217">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-217">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="6aeca-218">Compression Level</span><span class="sxs-lookup"><span data-stu-id="6aeca-218">Compression Level</span></span> | <span data-ttu-id="6aeca-219">描述</span><span class="sxs-lookup"><span data-stu-id="6aeca-219">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="6aeca-220">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="6aeca-220">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-221">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-221">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="6aeca-222">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="6aeca-222">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-223">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-223">No compression should be performed.</span></span> |
| [<span data-ttu-id="6aeca-224">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="6aeca-224">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-225">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-225">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="6aeca-226">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-226">Custom providers</span></span>

<span data-ttu-id="6aeca-227">通过创建自定义压缩实现 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-227">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="6aeca-228"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示此生成的内容编码 `ICompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-228">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="6aeca-229">中间件使用这些信息根据请求的标头中指定的列表来选择提供程序 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-229">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="6aeca-230">使用示例应用程序，客户端提交带有 `Accept-Encoding: mycustomcompression` 标头的请求。</span><span class="sxs-lookup"><span data-stu-id="6aeca-230">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6aeca-231">中间件使用自定义压缩实现并返回带有 `Content-Encoding: mycustomcompression` 标头的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-231">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6aeca-232">客户端必须能够解压缩自定义编码，才能使自定义压缩实现正常运行。</span><span class="sxs-lookup"><span data-stu-id="6aeca-232">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="6aeca-233">将请求提交到带有标头的示例应用 `Accept-Encoding: mycustomcompression` ，并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-233">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="6aeca-234">`Vary`和 `Content-Encoding` 标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-234">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="6aeca-235">该示例未压缩)  (显示的响应正文。</span><span class="sxs-lookup"><span data-stu-id="6aeca-235">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="6aeca-236">示例的类中没有压缩实现 `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-236">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="6aeca-237">但是，该示例显示了实现此类压缩算法的位置。</span><span class="sxs-lookup"><span data-stu-id="6aeca-237">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="6aeca-240">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="6aeca-240">MIME types</span></span>

<span data-ttu-id="6aeca-241">中间件为压缩指定了一组默认的 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="6aeca-241">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="6aeca-242">用响应压缩中间件选项替换或追加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="6aeca-242">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="6aeca-243">请注意，不支持通配符 MIME 类型，如 `text/*` 不支持。</span><span class="sxs-lookup"><span data-stu-id="6aeca-243">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="6aeca-244">该示例应用为添加了 MIME 类型 `image/svg+xml` ，并在 () *横幅*ASP.NET Core 中压缩并提供横幅图像。</span><span class="sxs-lookup"><span data-stu-id="6aeca-244">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="6aeca-245">安全协议压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-245">Compression with secure protocol</span></span>

<span data-ttu-id="6aeca-246">可以使用默认情况下禁用的选项控制安全连接上的压缩响应 `EnableForHttps` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-246">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="6aeca-247">在动态生成的页面中使用压缩可能会导致安全问题，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="6aeca-247">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="6aeca-248">添加 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="6aeca-248">Adding the Vary header</span></span>

<span data-ttu-id="6aeca-249">当基于标头压缩响应时 `Accept-Encoding` ，可能会有多个压缩版本的响应和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="6aeca-249">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="6aeca-250">为了指示客户端和代理缓存有多个版本存在且应该存储，将 `Vary` 使用一个值添加标头 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-250">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="6aeca-251">在 ASP.NET Core 2.0 或更高版本中，中间件 `Vary` 会在对响应进行压缩时自动添加标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-251">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="6aeca-252">Nginx 反向代理后的中间件问题</span><span class="sxs-lookup"><span data-stu-id="6aeca-252">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="6aeca-253">当 Nginx 代理请求时，将删除该 `Accept-Encoding` 标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-253">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="6aeca-254">删除 `Accept-Encoding` 标头会阻止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-254">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="6aeca-255">有关详细信息，请参阅[NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-255">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="6aeca-256">此问题通过[#123) 的 Nginx (的传递压缩来进行](https://github.com/aspnet/BasicMiddleware/issues/123)跟踪。</span><span class="sxs-lookup"><span data-stu-id="6aeca-256">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="6aeca-257">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-257">Working with IIS dynamic compression</span></span>

<span data-ttu-id="6aeca-258">如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块，请使用添加到*web.config*文件来禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="6aeca-258">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="6aeca-259">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-259">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6aeca-260">疑难解答</span><span class="sxs-lookup"><span data-stu-id="6aeca-260">Troubleshooting</span></span>

<span data-ttu-id="6aeca-261">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具，通过该工具，可以设置 `Accept-Encoding` 请求标头并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="6aeca-261">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="6aeca-262">默认情况下，响应压缩中间件会压缩满足以下条件的响应：</span><span class="sxs-lookup"><span data-stu-id="6aeca-262">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="6aeca-263">`Accept-Encoding`标头的值为 `br` 、 `gzip` 、 `*` 或自定义编码，它们与已建立的自定义压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="6aeca-263">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="6aeca-264">该值不能为 `identity` 或具有 (qvalue 的质量值， `q`) 设置为 0 (零) 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-264">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="6aeca-265">必须设置 MIME 类型 (`Content-Type`) 并且必须匹配在上配置的 mime 类型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-265">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="6aeca-266">请求不得包含 `Content-Range` 标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-266">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="6aeca-267">请求必须使用不安全协议 (http) ，除非在响应压缩中间件选项中配置了安全协议 (https) 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-267">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="6aeca-268">*启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="6aeca-268">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6aeca-269">其他资源</span><span class="sxs-lookup"><span data-stu-id="6aeca-269">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="6aeca-270">Mozilla 开发人员网络：接受编码</span><span class="sxs-lookup"><span data-stu-id="6aeca-270">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="6aeca-271">RFC 7231 部分3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="6aeca-271">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="6aeca-272">RFC 7230 部分4.2.3： Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="6aeca-272">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="6aeca-273">GZIP 文件格式规范版本4。3</span><span class="sxs-lookup"><span data-stu-id="6aeca-273">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6aeca-274">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="6aeca-274">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="6aeca-275">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="6aeca-275">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="6aeca-276">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-276">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="6aeca-277">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6aeca-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="6aeca-278">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="6aeca-278">When to use Response Compression Middleware</span></span>

<span data-ttu-id="6aeca-279">在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="6aeca-279">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="6aeca-280">中间件的性能与服务器模块的性能可能不一致。</span><span class="sxs-lookup"><span data-stu-id="6aeca-280">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="6aeca-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) Server 和[Kestrel](xref:fundamentals/servers/kestrel) server 目前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="6aeca-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="6aeca-282">使用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="6aeca-282">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="6aeca-283">无法使用以下基于服务器的压缩技术：</span><span class="sxs-lookup"><span data-stu-id="6aeca-283">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="6aeca-284">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="6aeca-284">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="6aeca-285">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="6aeca-285">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="6aeca-286">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-286">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="6aeca-287">直接在上托管：</span><span class="sxs-lookup"><span data-stu-id="6aeca-287">Hosting directly on:</span></span>
  * <span data-ttu-id="6aeca-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (以前称为 WebListener) </span><span class="sxs-lookup"><span data-stu-id="6aeca-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="6aeca-289">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="6aeca-289">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="6aeca-290">响应压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-290">Response compression</span></span>

<span data-ttu-id="6aeca-291">通常，任何未进行本机压缩的响应都可以从响应压缩中获益。</span><span class="sxs-lookup"><span data-stu-id="6aeca-291">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="6aeca-292">不以本机方式压缩的响应通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="6aeca-292">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="6aeca-293">不应压缩本机压缩的资产，例如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="6aeca-293">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="6aeca-294">如果尝试进一步压缩本机压缩的响应，则可能会相形见绌处理压缩所需的时间，从而减少大小和传输时间的任何小部分。</span><span class="sxs-lookup"><span data-stu-id="6aeca-294">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="6aeca-295">请勿压缩小于大约150-1000 字节的文件 (具体取决于文件的内容和压缩) 的效率。</span><span class="sxs-lookup"><span data-stu-id="6aeca-295">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="6aeca-296">压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="6aeca-296">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="6aeca-297">如果客户端可以处理压缩的内容，则客户端必须通过请求发送标头来通知服务器的功能 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-297">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="6aeca-298">当服务器发送压缩的内容时，它必须在标头中包含有关 `Content-Encoding` 如何对压缩的响应进行编码的信息。</span><span class="sxs-lookup"><span data-stu-id="6aeca-298">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="6aeca-299">下表显示了中间件支持的内容编码称号。</span><span class="sxs-lookup"><span data-stu-id="6aeca-299">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="6aeca-300">`Accept-Encoding`标头值</span><span class="sxs-lookup"><span data-stu-id="6aeca-300">`Accept-Encoding` header values</span></span> | <span data-ttu-id="6aeca-301">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="6aeca-301">Middleware Supported</span></span> | <span data-ttu-id="6aeca-302">描述</span><span class="sxs-lookup"><span data-stu-id="6aeca-302">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="6aeca-303">是（默认）</span><span class="sxs-lookup"><span data-stu-id="6aeca-303">Yes (default)</span></span>        | [<span data-ttu-id="6aeca-304">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-304">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="6aeca-305">否</span><span class="sxs-lookup"><span data-stu-id="6aeca-305">No</span></span>                   | [<span data-ttu-id="6aeca-306">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-306">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="6aeca-307">否</span><span class="sxs-lookup"><span data-stu-id="6aeca-307">No</span></span>                   | [<span data-ttu-id="6aeca-308">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="6aeca-308">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="6aeca-309">是</span><span class="sxs-lookup"><span data-stu-id="6aeca-309">Yes</span></span>                  | [<span data-ttu-id="6aeca-310">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-310">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="6aeca-311">是</span><span class="sxs-lookup"><span data-stu-id="6aeca-311">Yes</span></span>                  | <span data-ttu-id="6aeca-312">"无编码" 标识符：不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="6aeca-312">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="6aeca-313">否</span><span class="sxs-lookup"><span data-stu-id="6aeca-313">No</span></span>                   | [<span data-ttu-id="6aeca-314">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-314">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="6aeca-315">是</span><span class="sxs-lookup"><span data-stu-id="6aeca-315">Yes</span></span>                  | <span data-ttu-id="6aeca-316">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="6aeca-316">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="6aeca-317">有关详细信息，请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-317">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="6aeca-318">中间件允许您为自定义标头值添加其他压缩提供程序 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-318">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="6aeca-319">有关详细信息，请参阅下面的[自定义提供程序](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-319">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="6aeca-320">中间件能够对质量值 (qvalue， `q` 客户端发送时) 权重来确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="6aeca-320">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="6aeca-321">有关详细信息，请参阅[RFC 7231：接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-321">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="6aeca-322">压缩算法会在压缩速度与压缩效率之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="6aeca-322">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="6aeca-323">此上下文的*有效性*是指压缩后的输出大小。</span><span class="sxs-lookup"><span data-stu-id="6aeca-323">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="6aeca-324">最小大小是通过*最佳*压缩来实现的。</span><span class="sxs-lookup"><span data-stu-id="6aeca-324">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="6aeca-325">下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-325">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="6aeca-326">标头</span><span class="sxs-lookup"><span data-stu-id="6aeca-326">Header</span></span>             | <span data-ttu-id="6aeca-327">角色</span><span class="sxs-lookup"><span data-stu-id="6aeca-327">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="6aeca-328">从客户端发送到服务器，以指示客户端可接受的内容编码方案。</span><span class="sxs-lookup"><span data-stu-id="6aeca-328">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="6aeca-329">从服务器发送到客户端，以指示有效负载中内容的编码。</span><span class="sxs-lookup"><span data-stu-id="6aeca-329">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="6aeca-330">进行压缩时，将 `Content-Length` 删除该标头，因为在对响应进行压缩时，正文内容会发生更改。</span><span class="sxs-lookup"><span data-stu-id="6aeca-330">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="6aeca-331">进行压缩时，将 `Content-MD5` 删除该标头，因为正文内容已更改且哈希不再有效。</span><span class="sxs-lookup"><span data-stu-id="6aeca-331">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="6aeca-332">指定内容的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="6aeca-332">Specifies the MIME type of the content.</span></span> <span data-ttu-id="6aeca-333">每个响应都应指定其 `Content-Type` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-333">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="6aeca-334">中间件会检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-334">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="6aeca-335">中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types)，但你可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="6aeca-335">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="6aeca-336">当由服务器发送的值 `Accept-Encoding` 为 "客户端" 和 "代理服务器" 时， `Vary` 标头向客户端或代理发送应缓存 (根据 `Accept-Encoding` 请求标头的值改变) 响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-336">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="6aeca-337">将内容与标头一起返回的结果 `Vary: Accept-Encoding` 是：压缩的和未压缩的响应都单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="6aeca-337">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="6aeca-338">浏览用于[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。</span><span class="sxs-lookup"><span data-stu-id="6aeca-338">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="6aeca-339">该示例演示：</span><span class="sxs-lookup"><span data-stu-id="6aeca-339">The sample illustrates:</span></span>

* <span data-ttu-id="6aeca-340">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-340">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="6aeca-341">如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-341">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="6aeca-342">Package</span><span class="sxs-lookup"><span data-stu-id="6aeca-342">Package</span></span>

<span data-ttu-id="6aeca-343">若要将中间件包含在项目中，请添加对[AspNetCore 元包](xref:fundamentals/metapackage-app)的引用，其中包括[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。</span><span class="sxs-lookup"><span data-stu-id="6aeca-343">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="6aeca-344">配置</span><span class="sxs-lookup"><span data-stu-id="6aeca-344">Configuration</span></span>

<span data-ttu-id="6aeca-345">下面的代码演示如何为默认 MIME 类型和压缩提供程序 ([Brotli](#brotli-compression-provider)和[Gzip](#gzip-compression-provider)) 启用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="6aeca-345">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="6aeca-346">说明：</span><span class="sxs-lookup"><span data-stu-id="6aeca-346">Notes:</span></span>

* <span data-ttu-id="6aeca-347">`app.UseResponseCompression`必须在压缩响应的任何中间件前调用。</span><span class="sxs-lookup"><span data-stu-id="6aeca-347">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="6aeca-348">有关详细信息，请参阅 <xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="6aeca-348">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="6aeca-349">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具设置 `Accept-Encoding` 请求标头，并检查响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="6aeca-349">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="6aeca-350">在没有标头的情况下向示例应用程序提交请求 `Accept-Encoding` ，并观察响应是否未压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-350">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="6aeca-351">`Content-Encoding`响应中 `Vary` 不存在和标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-351">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口显示请求的结果，无需接受编码标头。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="6aeca-354">将请求提交到带有 `Accept-Encoding: br` 标头 (Brotli 压缩) 的示例应用，并观察响应是否已压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-354">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="6aeca-355">`Content-Encoding`和 `Vary` 标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-355">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值为 br 的请求的结果。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="6aeca-359">提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-359">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="6aeca-360">Brotli 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-360">Brotli Compression Provider</span></span>

<span data-ttu-id="6aeca-361">使用压缩 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 压缩数据格式](https://tools.ietf.org/html/rfc7932)的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-361">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="6aeca-362">如果没有将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="6aeca-362">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="6aeca-363">默认情况下，Brotli 压缩提供程序会随[Gzip 压缩提供程序](#gzip-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-363">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="6aeca-364">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-364">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="6aeca-365">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="6aeca-365">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="6aeca-366">在显式添加任何压缩提供程序时，必须添加 Brotli 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="6aeca-366">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6aeca-367">设置的压缩级别 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-367">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="6aeca-368">Brotli 压缩提供程序默认为最快的压缩级别 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) ，这可能不会产生最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-368">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="6aeca-369">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-369">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="6aeca-370">Compression Level</span><span class="sxs-lookup"><span data-stu-id="6aeca-370">Compression Level</span></span> | <span data-ttu-id="6aeca-371">描述</span><span class="sxs-lookup"><span data-stu-id="6aeca-371">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="6aeca-372">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="6aeca-372">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-373">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-373">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="6aeca-374">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="6aeca-374">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-375">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-375">No compression should be performed.</span></span> |
| [<span data-ttu-id="6aeca-376">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="6aeca-376">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-377">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-377">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a><span data-ttu-id="6aeca-378">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-378">Gzip Compression Provider</span></span>

<span data-ttu-id="6aeca-379">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-379">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="6aeca-380">如果没有将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="6aeca-380">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="6aeca-381">默认情况下，Gzip 压缩提供程序随[Brotli 压缩提供程序](#brotli-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-381">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="6aeca-382">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-382">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="6aeca-383">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="6aeca-383">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="6aeca-384">在显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="6aeca-384">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="6aeca-385">设置的压缩级别 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-385">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="6aeca-386">Gzip 压缩提供程序默认为最快的压缩级别 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) ，这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-386">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="6aeca-387">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-387">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="6aeca-388">Compression Level</span><span class="sxs-lookup"><span data-stu-id="6aeca-388">Compression Level</span></span> | <span data-ttu-id="6aeca-389">描述</span><span class="sxs-lookup"><span data-stu-id="6aeca-389">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="6aeca-390">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="6aeca-390">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-391">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-391">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="6aeca-392">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="6aeca-392">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-393">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-393">No compression should be performed.</span></span> |
| [<span data-ttu-id="6aeca-394">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="6aeca-394">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-395">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-395">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="6aeca-396">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-396">Custom providers</span></span>

<span data-ttu-id="6aeca-397">通过创建自定义压缩实现 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-397">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="6aeca-398"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示此生成的内容编码 `ICompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-398">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="6aeca-399">中间件使用这些信息根据请求的标头中指定的列表来选择提供程序 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-399">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="6aeca-400">使用示例应用程序，客户端提交带有 `Accept-Encoding: mycustomcompression` 标头的请求。</span><span class="sxs-lookup"><span data-stu-id="6aeca-400">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6aeca-401">中间件使用自定义压缩实现并返回带有 `Content-Encoding: mycustomcompression` 标头的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-401">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6aeca-402">客户端必须能够解压缩自定义编码，才能使自定义压缩实现正常运行。</span><span class="sxs-lookup"><span data-stu-id="6aeca-402">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="6aeca-403">将请求提交到带有标头的示例应用 `Accept-Encoding: mycustomcompression` ，并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-403">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="6aeca-404">`Vary`和 `Content-Encoding` 标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-404">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="6aeca-405">该示例未压缩)  (显示的响应正文。</span><span class="sxs-lookup"><span data-stu-id="6aeca-405">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="6aeca-406">示例的类中没有压缩实现 `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-406">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="6aeca-407">但是，该示例显示了实现此类压缩算法的位置。</span><span class="sxs-lookup"><span data-stu-id="6aeca-407">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="6aeca-410">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="6aeca-410">MIME types</span></span>

<span data-ttu-id="6aeca-411">中间件为压缩指定了一组默认的 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="6aeca-411">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="6aeca-412">用响应压缩中间件选项替换或追加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="6aeca-412">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="6aeca-413">请注意，不支持通配符 MIME 类型，如 `text/*` 不支持。</span><span class="sxs-lookup"><span data-stu-id="6aeca-413">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="6aeca-414">该示例应用为添加了 MIME 类型 `image/svg+xml` ，并在 () *横幅*ASP.NET Core 中压缩并提供横幅图像。</span><span class="sxs-lookup"><span data-stu-id="6aeca-414">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="6aeca-415">安全协议压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-415">Compression with secure protocol</span></span>

<span data-ttu-id="6aeca-416">可以使用默认情况下禁用的选项控制安全连接上的压缩响应 `EnableForHttps` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-416">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="6aeca-417">在动态生成的页面中使用压缩可能会导致安全问题，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="6aeca-417">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="6aeca-418">添加 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="6aeca-418">Adding the Vary header</span></span>

<span data-ttu-id="6aeca-419">当基于标头压缩响应时 `Accept-Encoding` ，可能会有多个压缩版本的响应和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="6aeca-419">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="6aeca-420">为了指示客户端和代理缓存有多个版本存在且应该存储，将 `Vary` 使用一个值添加标头 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-420">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="6aeca-421">在 ASP.NET Core 2.0 或更高版本中，中间件 `Vary` 会在对响应进行压缩时自动添加标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-421">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="6aeca-422">Nginx 反向代理后的中间件问题</span><span class="sxs-lookup"><span data-stu-id="6aeca-422">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="6aeca-423">当 Nginx 代理请求时，将删除该 `Accept-Encoding` 标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-423">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="6aeca-424">删除 `Accept-Encoding` 标头会阻止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-424">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="6aeca-425">有关详细信息，请参阅[NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-425">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="6aeca-426">此问题通过[#123) 的 Nginx (的传递压缩来进行](https://github.com/aspnet/BasicMiddleware/issues/123)跟踪。</span><span class="sxs-lookup"><span data-stu-id="6aeca-426">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="6aeca-427">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-427">Working with IIS dynamic compression</span></span>

<span data-ttu-id="6aeca-428">如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块，请使用添加到*web.config*文件来禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="6aeca-428">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="6aeca-429">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-429">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6aeca-430">疑难解答</span><span class="sxs-lookup"><span data-stu-id="6aeca-430">Troubleshooting</span></span>

<span data-ttu-id="6aeca-431">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具，通过该工具，可以设置 `Accept-Encoding` 请求标头并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="6aeca-431">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="6aeca-432">默认情况下，响应压缩中间件会压缩满足以下条件的响应：</span><span class="sxs-lookup"><span data-stu-id="6aeca-432">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="6aeca-433">`Accept-Encoding`标头的值为 `br` 、 `gzip` 、 `*` 或自定义编码，它们与已建立的自定义压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="6aeca-433">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="6aeca-434">该值不能为 `identity` 或具有 (qvalue 的质量值， `q`) 设置为 0 (零) 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-434">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="6aeca-435">必须设置 MIME 类型 (`Content-Type`) 并且必须匹配在上配置的 mime 类型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-435">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="6aeca-436">请求不得包含 `Content-Range` 标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-436">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="6aeca-437">请求必须使用不安全协议 (http) ，除非在响应压缩中间件选项中配置了安全协议 (https) 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-437">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="6aeca-438">*启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="6aeca-438">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6aeca-439">其他资源</span><span class="sxs-lookup"><span data-stu-id="6aeca-439">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="6aeca-440">Mozilla 开发人员网络：接受编码</span><span class="sxs-lookup"><span data-stu-id="6aeca-440">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="6aeca-441">RFC 7231 部分3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="6aeca-441">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="6aeca-442">RFC 7230 部分4.2.3： Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="6aeca-442">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="6aeca-443">GZIP 文件格式规范版本4。3</span><span class="sxs-lookup"><span data-stu-id="6aeca-443">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6aeca-444">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="6aeca-444">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="6aeca-445">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="6aeca-445">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="6aeca-446">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-446">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="6aeca-447">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6aeca-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="6aeca-448">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="6aeca-448">When to use Response Compression Middleware</span></span>

<span data-ttu-id="6aeca-449">在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="6aeca-449">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="6aeca-450">中间件的性能与服务器模块的性能可能不一致。</span><span class="sxs-lookup"><span data-stu-id="6aeca-450">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="6aeca-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) Server 和[Kestrel](xref:fundamentals/servers/kestrel) server 目前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="6aeca-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="6aeca-452">使用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="6aeca-452">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="6aeca-453">无法使用以下基于服务器的压缩技术：</span><span class="sxs-lookup"><span data-stu-id="6aeca-453">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="6aeca-454">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="6aeca-454">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="6aeca-455">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="6aeca-455">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="6aeca-456">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-456">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="6aeca-457">直接在上托管：</span><span class="sxs-lookup"><span data-stu-id="6aeca-457">Hosting directly on:</span></span>
  * <span data-ttu-id="6aeca-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (以前称为 WebListener) </span><span class="sxs-lookup"><span data-stu-id="6aeca-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="6aeca-459">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="6aeca-459">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="6aeca-460">响应压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-460">Response compression</span></span>

<span data-ttu-id="6aeca-461">通常，任何未进行本机压缩的响应都可以从响应压缩中获益。</span><span class="sxs-lookup"><span data-stu-id="6aeca-461">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="6aeca-462">不以本机方式压缩的响应通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="6aeca-462">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="6aeca-463">不应压缩本机压缩的资产，例如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="6aeca-463">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="6aeca-464">如果尝试进一步压缩本机压缩的响应，则可能会相形见绌处理压缩所需的时间，从而减少大小和传输时间的任何小部分。</span><span class="sxs-lookup"><span data-stu-id="6aeca-464">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="6aeca-465">请勿压缩小于大约150-1000 字节的文件 (具体取决于文件的内容和压缩) 的效率。</span><span class="sxs-lookup"><span data-stu-id="6aeca-465">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="6aeca-466">压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="6aeca-466">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="6aeca-467">如果客户端可以处理压缩的内容，则客户端必须通过请求发送标头来通知服务器的功能 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-467">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="6aeca-468">当服务器发送压缩的内容时，它必须在标头中包含有关 `Content-Encoding` 如何对压缩的响应进行编码的信息。</span><span class="sxs-lookup"><span data-stu-id="6aeca-468">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="6aeca-469">下表显示了中间件支持的内容编码称号。</span><span class="sxs-lookup"><span data-stu-id="6aeca-469">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="6aeca-470">`Accept-Encoding`标头值</span><span class="sxs-lookup"><span data-stu-id="6aeca-470">`Accept-Encoding` header values</span></span> | <span data-ttu-id="6aeca-471">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="6aeca-471">Middleware Supported</span></span> | <span data-ttu-id="6aeca-472">描述</span><span class="sxs-lookup"><span data-stu-id="6aeca-472">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="6aeca-473">否</span><span class="sxs-lookup"><span data-stu-id="6aeca-473">No</span></span>                   | [<span data-ttu-id="6aeca-474">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-474">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="6aeca-475">否</span><span class="sxs-lookup"><span data-stu-id="6aeca-475">No</span></span>                   | [<span data-ttu-id="6aeca-476">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-476">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="6aeca-477">否</span><span class="sxs-lookup"><span data-stu-id="6aeca-477">No</span></span>                   | [<span data-ttu-id="6aeca-478">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="6aeca-478">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="6aeca-479">是（默认）</span><span class="sxs-lookup"><span data-stu-id="6aeca-479">Yes (default)</span></span>        | [<span data-ttu-id="6aeca-480">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-480">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="6aeca-481">是</span><span class="sxs-lookup"><span data-stu-id="6aeca-481">Yes</span></span>                  | <span data-ttu-id="6aeca-482">"无编码" 标识符：不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="6aeca-482">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="6aeca-483">否</span><span class="sxs-lookup"><span data-stu-id="6aeca-483">No</span></span>                   | [<span data-ttu-id="6aeca-484">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="6aeca-484">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="6aeca-485">是</span><span class="sxs-lookup"><span data-stu-id="6aeca-485">Yes</span></span>                  | <span data-ttu-id="6aeca-486">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="6aeca-486">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="6aeca-487">有关详细信息，请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-487">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="6aeca-488">中间件允许您为自定义标头值添加其他压缩提供程序 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-488">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="6aeca-489">有关详细信息，请参阅下面的[自定义提供程序](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-489">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="6aeca-490">中间件能够对质量值 (qvalue， `q` 客户端发送时) 权重来确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="6aeca-490">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="6aeca-491">有关详细信息，请参阅[RFC 7231：接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-491">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="6aeca-492">压缩算法会在压缩速度与压缩效率之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="6aeca-492">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="6aeca-493">此上下文的*有效性*是指压缩后的输出大小。</span><span class="sxs-lookup"><span data-stu-id="6aeca-493">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="6aeca-494">最小大小是通过*最佳*压缩来实现的。</span><span class="sxs-lookup"><span data-stu-id="6aeca-494">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="6aeca-495">下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-495">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="6aeca-496">标头</span><span class="sxs-lookup"><span data-stu-id="6aeca-496">Header</span></span>             | <span data-ttu-id="6aeca-497">角色</span><span class="sxs-lookup"><span data-stu-id="6aeca-497">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="6aeca-498">从客户端发送到服务器，以指示客户端可接受的内容编码方案。</span><span class="sxs-lookup"><span data-stu-id="6aeca-498">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="6aeca-499">从服务器发送到客户端，以指示有效负载中内容的编码。</span><span class="sxs-lookup"><span data-stu-id="6aeca-499">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="6aeca-500">进行压缩时，将 `Content-Length` 删除该标头，因为在对响应进行压缩时，正文内容会发生更改。</span><span class="sxs-lookup"><span data-stu-id="6aeca-500">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="6aeca-501">进行压缩时，将 `Content-MD5` 删除该标头，因为正文内容已更改且哈希不再有效。</span><span class="sxs-lookup"><span data-stu-id="6aeca-501">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="6aeca-502">指定内容的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="6aeca-502">Specifies the MIME type of the content.</span></span> <span data-ttu-id="6aeca-503">每个响应都应指定其 `Content-Type` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-503">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="6aeca-504">中间件会检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-504">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="6aeca-505">中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types)，但你可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="6aeca-505">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="6aeca-506">当由服务器发送的值 `Accept-Encoding` 为 "客户端" 和 "代理服务器" 时， `Vary` 标头向客户端或代理发送应缓存 (根据 `Accept-Encoding` 请求标头的值改变) 响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-506">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="6aeca-507">将内容与标头一起返回的结果 `Vary: Accept-Encoding` 是：压缩的和未压缩的响应都单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="6aeca-507">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="6aeca-508">浏览用于[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。</span><span class="sxs-lookup"><span data-stu-id="6aeca-508">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="6aeca-509">该示例演示：</span><span class="sxs-lookup"><span data-stu-id="6aeca-509">The sample illustrates:</span></span>

* <span data-ttu-id="6aeca-510">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-510">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="6aeca-511">如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-511">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="6aeca-512">Package</span><span class="sxs-lookup"><span data-stu-id="6aeca-512">Package</span></span>

<span data-ttu-id="6aeca-513">若要将中间件包含在项目中，请添加对[AspNetCore 元包](xref:fundamentals/metapackage-app)的引用，其中包括[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。</span><span class="sxs-lookup"><span data-stu-id="6aeca-513">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="6aeca-514">配置</span><span class="sxs-lookup"><span data-stu-id="6aeca-514">Configuration</span></span>

<span data-ttu-id="6aeca-515">下面的代码演示如何为默认 MIME 类型和[Gzip 压缩提供程序](#gzip-compression-provider)启用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="6aeca-515">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="6aeca-516">说明：</span><span class="sxs-lookup"><span data-stu-id="6aeca-516">Notes:</span></span>

* <span data-ttu-id="6aeca-517">`app.UseResponseCompression`必须在压缩响应的任何中间件前调用。</span><span class="sxs-lookup"><span data-stu-id="6aeca-517">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="6aeca-518">有关详细信息，请参阅 <xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="6aeca-518">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="6aeca-519">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具设置 `Accept-Encoding` 请求标头，并检查响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="6aeca-519">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="6aeca-520">在没有标头的情况下向示例应用程序提交请求 `Accept-Encoding` ，并观察响应是否未压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-520">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="6aeca-521">`Content-Encoding`响应中 `Vary` 不存在和标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-521">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口显示请求的结果，无需接受编码标头。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="6aeca-524">将请求提交到带有标头的示例应用 `Accept-Encoding: gzip` ，并观察是否已压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-524">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="6aeca-525">`Content-Encoding`和 `Vary` 标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-525">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![显示带有接受编码标头和值 gzip 的请求结果的 Fiddler 窗口。](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="6aeca-529">提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-529">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="6aeca-530">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-530">Gzip Compression Provider</span></span>

<span data-ttu-id="6aeca-531">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-531">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="6aeca-532">如果没有将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> ：</span><span class="sxs-lookup"><span data-stu-id="6aeca-532">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="6aeca-533">默认情况下，将 Gzip 压缩提供程序添加到压缩提供程序的数组。</span><span class="sxs-lookup"><span data-stu-id="6aeca-533">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="6aeca-534">当客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="6aeca-534">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="6aeca-535">在显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="6aeca-535">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="6aeca-536">设置的压缩级别 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-536">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="6aeca-537">Gzip 压缩提供程序默认为最快的压缩级别 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) ，这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-537">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="6aeca-538">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-538">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="6aeca-539">Compression Level</span><span class="sxs-lookup"><span data-stu-id="6aeca-539">Compression Level</span></span> | <span data-ttu-id="6aeca-540">描述</span><span class="sxs-lookup"><span data-stu-id="6aeca-540">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="6aeca-541">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="6aeca-541">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-542">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-542">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="6aeca-543">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="6aeca-543">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-544">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="6aeca-544">No compression should be performed.</span></span> |
| [<span data-ttu-id="6aeca-545">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="6aeca-545">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="6aeca-546">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-546">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="6aeca-547">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="6aeca-547">Custom providers</span></span>

<span data-ttu-id="6aeca-548">通过创建自定义压缩实现 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-548">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="6aeca-549"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示此生成的内容编码 `ICompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-549">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="6aeca-550">中间件使用这些信息根据请求的标头中指定的列表来选择提供程序 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-550">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="6aeca-551">使用示例应用程序，客户端提交带有 `Accept-Encoding: mycustomcompression` 标头的请求。</span><span class="sxs-lookup"><span data-stu-id="6aeca-551">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6aeca-552">中间件使用自定义压缩实现并返回带有 `Content-Encoding: mycustomcompression` 标头的响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-552">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="6aeca-553">客户端必须能够解压缩自定义编码，才能使自定义压缩实现正常运行。</span><span class="sxs-lookup"><span data-stu-id="6aeca-553">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="6aeca-554">将请求提交到带有标头的示例应用 `Accept-Encoding: mycustomcompression` ，并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-554">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="6aeca-555">`Vary`和 `Content-Encoding` 标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="6aeca-555">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="6aeca-556">该示例未压缩)  (显示的响应正文。</span><span class="sxs-lookup"><span data-stu-id="6aeca-556">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="6aeca-557">示例的类中没有压缩实现 `CustomCompressionProvider` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-557">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="6aeca-558">但是，该示例显示了实现此类压缩算法的位置。</span><span class="sxs-lookup"><span data-stu-id="6aeca-558">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="6aeca-561">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="6aeca-561">MIME types</span></span>

<span data-ttu-id="6aeca-562">中间件为压缩指定了一组默认的 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="6aeca-562">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="6aeca-563">用响应压缩中间件选项替换或追加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="6aeca-563">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="6aeca-564">请注意，不支持通配符 MIME 类型，如 `text/*` 不支持。</span><span class="sxs-lookup"><span data-stu-id="6aeca-564">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="6aeca-565">该示例应用为添加了 MIME 类型 `image/svg+xml` ，并在 () *横幅*ASP.NET Core 中压缩并提供横幅图像。</span><span class="sxs-lookup"><span data-stu-id="6aeca-565">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="6aeca-566">安全协议压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-566">Compression with secure protocol</span></span>

<span data-ttu-id="6aeca-567">可以使用默认情况下禁用的选项控制安全连接上的压缩响应 `EnableForHttps` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-567">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="6aeca-568">在动态生成的页面中使用压缩可能会导致安全问题，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="6aeca-568">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="6aeca-569">添加 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="6aeca-569">Adding the Vary header</span></span>

<span data-ttu-id="6aeca-570">当基于标头压缩响应时 `Accept-Encoding` ，可能会有多个压缩版本的响应和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="6aeca-570">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="6aeca-571">为了指示客户端和代理缓存有多个版本存在且应该存储，将 `Vary` 使用一个值添加标头 `Accept-Encoding` 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-571">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="6aeca-572">在 ASP.NET Core 2.0 或更高版本中，中间件 `Vary` 会在对响应进行压缩时自动添加标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-572">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="6aeca-573">Nginx 反向代理后的中间件问题</span><span class="sxs-lookup"><span data-stu-id="6aeca-573">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="6aeca-574">当 Nginx 代理请求时，将删除该 `Accept-Encoding` 标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-574">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="6aeca-575">删除 `Accept-Encoding` 标头会阻止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="6aeca-575">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="6aeca-576">有关详细信息，请参阅[NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-576">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="6aeca-577">此问题通过[#123) 的 Nginx (的传递压缩来进行](https://github.com/aspnet/BasicMiddleware/issues/123)跟踪。</span><span class="sxs-lookup"><span data-stu-id="6aeca-577">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="6aeca-578">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="6aeca-578">Working with IIS dynamic compression</span></span>

<span data-ttu-id="6aeca-579">如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块，请使用添加到*web.config*文件来禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="6aeca-579">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="6aeca-580">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="6aeca-580">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6aeca-581">疑难解答</span><span class="sxs-lookup"><span data-stu-id="6aeca-581">Troubleshooting</span></span>

<span data-ttu-id="6aeca-582">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具，通过该工具，可以设置 `Accept-Encoding` 请求标头并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="6aeca-582">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="6aeca-583">默认情况下，响应压缩中间件会压缩满足以下条件的响应：</span><span class="sxs-lookup"><span data-stu-id="6aeca-583">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="6aeca-584">`Accept-Encoding`标头包含的值为 `gzip` 、 `*` 或自定义编码与已建立的自定义压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="6aeca-584">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="6aeca-585">该值不能为 `identity` 或具有 (qvalue 的质量值， `q`) 设置为 0 (零) 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-585">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="6aeca-586">必须设置 MIME 类型 (`Content-Type`) 并且必须匹配在上配置的 mime 类型 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-586">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="6aeca-587">请求不得包含 `Content-Range` 标头。</span><span class="sxs-lookup"><span data-stu-id="6aeca-587">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="6aeca-588">请求必须使用不安全协议 (http) ，除非在响应压缩中间件选项中配置了安全协议 (https) 。</span><span class="sxs-lookup"><span data-stu-id="6aeca-588">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="6aeca-589">*启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="6aeca-589">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6aeca-590">其他资源</span><span class="sxs-lookup"><span data-stu-id="6aeca-590">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="6aeca-591">Mozilla 开发人员网络：接受编码</span><span class="sxs-lookup"><span data-stu-id="6aeca-591">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="6aeca-592">RFC 7231 部分3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="6aeca-592">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="6aeca-593">RFC 7230 部分4.2.3： Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="6aeca-593">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="6aeca-594">GZIP 文件格式规范版本4。3</span><span class="sxs-lookup"><span data-stu-id="6aeca-594">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
