---
title: ASP.NET Core 中的响应压缩
author: rick-anderson
description: 了解响应压缩以及如何在 ASP.NET Core 应用中使用响应压缩中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/response-compression
ms.openlocfilehash: 8fc68f2303bfcf16d279b829ab9441a80119f1bb
ms.sourcegitcommit: 755952496316fdb0923689109b536b609ce525ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82643082"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="bb379-103">ASP.NET Core 中的响应压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-103">Response compression in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bb379-104">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="bb379-104">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="bb379-105">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="bb379-105">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="bb379-106">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-106">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="bb379-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="bb379-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="bb379-108">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="bb379-108">When to use Response Compression Middleware</span></span>

<span data-ttu-id="bb379-109">在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="bb379-109">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="bb379-110">中间件的性能与服务器模块的性能可能不一致。</span><span class="sxs-lookup"><span data-stu-id="bb379-110">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="bb379-111">[Http.sys 服务器](xref:fundamentals/servers/httpsys)服务器和[Kestrel](xref:fundamentals/servers/kestrel)服务器当前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="bb379-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="bb379-112">使用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="bb379-112">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="bb379-113">无法使用以下基于服务器的压缩技术：</span><span class="sxs-lookup"><span data-stu-id="bb379-113">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="bb379-114">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="bb379-114">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="bb379-115">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="bb379-115">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="bb379-116">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-116">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="bb379-117">直接在上托管：</span><span class="sxs-lookup"><span data-stu-id="bb379-117">Hosting directly on:</span></span>
  * <span data-ttu-id="bb379-118">[Http.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）</span><span class="sxs-lookup"><span data-stu-id="bb379-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="bb379-119">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="bb379-119">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="bb379-120">响应压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-120">Response compression</span></span>

<span data-ttu-id="bb379-121">通常，任何未进行本机压缩的响应都可以从响应压缩中获益。</span><span class="sxs-lookup"><span data-stu-id="bb379-121">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="bb379-122">不以本机方式压缩的响应通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="bb379-122">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="bb379-123">不应压缩本机压缩的资产，例如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="bb379-123">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="bb379-124">如果尝试进一步压缩本机压缩的响应，则可能会相形见绌处理压缩所需的时间，从而减少大小和传输时间的任何小部分。</span><span class="sxs-lookup"><span data-stu-id="bb379-124">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="bb379-125">不要压缩小于大约150-1000 字节的文件（具体取决于文件的内容和压缩的效率）。</span><span class="sxs-lookup"><span data-stu-id="bb379-125">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="bb379-126">压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="bb379-126">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="bb379-127">如果客户端可以处理压缩的内容，则客户端必须通过请求发送`Accept-Encoding`标头来通知服务器的功能。</span><span class="sxs-lookup"><span data-stu-id="bb379-127">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="bb379-128">当服务器发送压缩的内容时，它必须在`Content-Encoding`标头中包含有关如何对压缩的响应进行编码的信息。</span><span class="sxs-lookup"><span data-stu-id="bb379-128">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="bb379-129">下表显示了中间件支持的内容编码称号。</span><span class="sxs-lookup"><span data-stu-id="bb379-129">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="bb379-130">`Accept-Encoding`标头值</span><span class="sxs-lookup"><span data-stu-id="bb379-130">`Accept-Encoding` header values</span></span> | <span data-ttu-id="bb379-131">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="bb379-131">Middleware Supported</span></span> | <span data-ttu-id="bb379-132">说明</span><span class="sxs-lookup"><span data-stu-id="bb379-132">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="bb379-133">是（默认）</span><span class="sxs-lookup"><span data-stu-id="bb379-133">Yes (default)</span></span>        | [<span data-ttu-id="bb379-134">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="bb379-134">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="bb379-135">否</span><span class="sxs-lookup"><span data-stu-id="bb379-135">No</span></span>                   | [<span data-ttu-id="bb379-136">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="bb379-136">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="bb379-137">否</span><span class="sxs-lookup"><span data-stu-id="bb379-137">No</span></span>                   | [<span data-ttu-id="bb379-138">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="bb379-138">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="bb379-139">是</span><span class="sxs-lookup"><span data-stu-id="bb379-139">Yes</span></span>                  | [<span data-ttu-id="bb379-140">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="bb379-140">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="bb379-141">是</span><span class="sxs-lookup"><span data-stu-id="bb379-141">Yes</span></span>                  | <span data-ttu-id="bb379-142">"无编码" 标识符：不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="bb379-142">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="bb379-143">否</span><span class="sxs-lookup"><span data-stu-id="bb379-143">No</span></span>                   | [<span data-ttu-id="bb379-144">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="bb379-144">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="bb379-145">是</span><span class="sxs-lookup"><span data-stu-id="bb379-145">Yes</span></span>                  | <span data-ttu-id="bb379-146">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="bb379-146">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="bb379-147">有关详细信息，请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="bb379-147">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="bb379-148">中间件允许您为自定义`Accept-Encoding`标头值添加其他压缩提供程序。</span><span class="sxs-lookup"><span data-stu-id="bb379-148">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="bb379-149">有关详细信息，请参阅下面的[自定义提供程序](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="bb379-149">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="bb379-150">当客户端发送时，中间件能够对质量值`q`（qvalue，）加权作出反应，以确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="bb379-150">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="bb379-151">有关详细信息，请参阅[RFC 7231：接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="bb379-151">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="bb379-152">压缩算法会在压缩速度与压缩效率之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="bb379-152">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="bb379-153">此上下文的*有效性*是指压缩后的输出大小。</span><span class="sxs-lookup"><span data-stu-id="bb379-153">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="bb379-154">最小大小是通过*最佳*压缩来实现的。</span><span class="sxs-lookup"><span data-stu-id="bb379-154">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="bb379-155">下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-155">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="bb379-156">Header</span><span class="sxs-lookup"><span data-stu-id="bb379-156">Header</span></span>             | <span data-ttu-id="bb379-157">Role</span><span class="sxs-lookup"><span data-stu-id="bb379-157">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="bb379-158">从客户端发送到服务器，以指示客户端可接受的内容编码方案。</span><span class="sxs-lookup"><span data-stu-id="bb379-158">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="bb379-159">从服务器发送到客户端，以指示有效负载中内容的编码。</span><span class="sxs-lookup"><span data-stu-id="bb379-159">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="bb379-160">进行压缩时，将`Content-Length`删除该标头，因为在对响应进行压缩时，正文内容会发生更改。</span><span class="sxs-lookup"><span data-stu-id="bb379-160">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="bb379-161">进行压缩时，将`Content-MD5`删除该标头，因为正文内容已更改且哈希不再有效。</span><span class="sxs-lookup"><span data-stu-id="bb379-161">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="bb379-162">指定内容的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="bb379-162">Specifies the MIME type of the content.</span></span> <span data-ttu-id="bb379-163">每个响应都应`Content-Type`指定其。</span><span class="sxs-lookup"><span data-stu-id="bb379-163">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="bb379-164">中间件会检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-164">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="bb379-165">中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types)，但你可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="bb379-165">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="bb379-166">当服务器将值为的`Accept-Encoding`客户端和代理服务器发送时， `Vary`标头将向客户端或代理发送根据请求`Accept-Encoding`标头的值来缓存（变化）的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-166">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="bb379-167">将内容与`Vary: Accept-Encoding`标头一起返回的结果是：压缩的和未压缩的响应都单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="bb379-167">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="bb379-168">浏览用于[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。</span><span class="sxs-lookup"><span data-stu-id="bb379-168">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="bb379-169">该示例演示：</span><span class="sxs-lookup"><span data-stu-id="bb379-169">The sample illustrates:</span></span>

* <span data-ttu-id="bb379-170">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-170">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="bb379-171">如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-171">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="bb379-172">包</span><span class="sxs-lookup"><span data-stu-id="bb379-172">Package</span></span>

<span data-ttu-id="bb379-173">响应压缩中间件由[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包提供，后者隐式包含在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="bb379-173">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="bb379-174">配置</span><span class="sxs-lookup"><span data-stu-id="bb379-174">Configuration</span></span>

<span data-ttu-id="bb379-175">下面的代码演示如何为默认 MIME 类型和压缩提供程序（[Brotli](#brotli-compression-provider)和[Gzip](#gzip-compression-provider)）启用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="bb379-175">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="bb379-176">注意：</span><span class="sxs-lookup"><span data-stu-id="bb379-176">Notes:</span></span>

* <span data-ttu-id="bb379-177">`app.UseResponseCompression`必须在压缩响应的任何中间件前调用。</span><span class="sxs-lookup"><span data-stu-id="bb379-177">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="bb379-178">有关详细信息，请参阅 <xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="bb379-178">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="bb379-179">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具设置`Accept-Encoding`请求标头，并检查响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="bb379-179">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="bb379-180">在没有`Accept-Encoding`标头的情况下向示例应用程序提交请求，并观察响应是否未压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-180">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="bb379-181">响应`Content-Encoding`中`Vary`不存在和标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-181">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口显示请求的结果，无需接受编码标头。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="bb379-184">将请求提交到带有`Accept-Encoding: br`标头的示例应用（Brotli 压缩），并观察响应是否已压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-184">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="bb379-185">`Content-Encoding`和`Vary`标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="bb379-185">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值为 br 的请求的结果。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="bb379-189">提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-189">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="bb379-190">Brotli 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-190">Brotli Compression Provider</span></span>

<span data-ttu-id="bb379-191"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>使用压缩[Brotli 压缩数据格式](https://tools.ietf.org/html/rfc7932)的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-191">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="bb379-192">如果没有将压缩提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：</span><span class="sxs-lookup"><span data-stu-id="bb379-192">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="bb379-193">默认情况下，Brotli 压缩提供程序会随[Gzip 压缩提供程序](#gzip-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="bb379-193">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="bb379-194">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-194">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="bb379-195">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="bb379-195">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="bb379-196">在显式添加任何压缩提供程序时，必须添加 Brotli 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="bb379-196">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bb379-197">设置的压缩级别<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>。</span><span class="sxs-lookup"><span data-stu-id="bb379-197">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="bb379-198">Brotli 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-198">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="bb379-199">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-199">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="bb379-200">Compression Level</span><span class="sxs-lookup"><span data-stu-id="bb379-200">Compression Level</span></span> | <span data-ttu-id="bb379-201">说明</span><span class="sxs-lookup"><span data-stu-id="bb379-201">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="bb379-202">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="bb379-202">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-203">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-203">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="bb379-204">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="bb379-204">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-205">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-205">No compression should be performed.</span></span> |
| [<span data-ttu-id="bb379-206">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="bb379-206">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-207">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-207">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="bb379-208">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-208">Gzip Compression Provider</span></span>

<span data-ttu-id="bb379-209"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>使用压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-209">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="bb379-210">如果没有将压缩提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：</span><span class="sxs-lookup"><span data-stu-id="bb379-210">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="bb379-211">默认情况下，Gzip 压缩提供程序随[Brotli 压缩提供程序](#brotli-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="bb379-211">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="bb379-212">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-212">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="bb379-213">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="bb379-213">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="bb379-214">在显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="bb379-214">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="bb379-215">设置的压缩级别<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>。</span><span class="sxs-lookup"><span data-stu-id="bb379-215">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="bb379-216">Gzip 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-216">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="bb379-217">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-217">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="bb379-218">Compression Level</span><span class="sxs-lookup"><span data-stu-id="bb379-218">Compression Level</span></span> | <span data-ttu-id="bb379-219">说明</span><span class="sxs-lookup"><span data-stu-id="bb379-219">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="bb379-220">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="bb379-220">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-221">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-221">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="bb379-222">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="bb379-222">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-223">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-223">No compression should be performed.</span></span> |
| [<span data-ttu-id="bb379-224">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="bb379-224">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-225">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-225">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="bb379-226">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-226">Custom providers</span></span>

<span data-ttu-id="bb379-227">通过<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>创建自定义压缩实现。</span><span class="sxs-lookup"><span data-stu-id="bb379-227">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="bb379-228"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示此`ICompressionProvider`生成的内容编码。</span><span class="sxs-lookup"><span data-stu-id="bb379-228">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="bb379-229">中间件使用这些信息根据请求的`Accept-Encoding`标头中指定的列表来选择提供程序。</span><span class="sxs-lookup"><span data-stu-id="bb379-229">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="bb379-230">使用示例应用程序，客户端提交带有`Accept-Encoding: mycustomcompression`标头的请求。</span><span class="sxs-lookup"><span data-stu-id="bb379-230">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="bb379-231">中间件使用自定义压缩实现并返回带有`Content-Encoding: mycustomcompression`标头的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-231">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="bb379-232">客户端必须能够解压缩自定义编码，才能使自定义压缩实现正常运行。</span><span class="sxs-lookup"><span data-stu-id="bb379-232">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="bb379-233">将请求提交到带有`Accept-Encoding: mycustomcompression`标头的示例应用，并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-233">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="bb379-234">`Vary`和`Content-Encoding`标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="bb379-234">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="bb379-235">示例不压缩响应正文（未显示）。</span><span class="sxs-lookup"><span data-stu-id="bb379-235">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="bb379-236">示例的`CustomCompressionProvider`类中没有压缩实现。</span><span class="sxs-lookup"><span data-stu-id="bb379-236">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="bb379-237">但是，该示例显示了实现此类压缩算法的位置。</span><span class="sxs-lookup"><span data-stu-id="bb379-237">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="bb379-240">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="bb379-240">MIME types</span></span>

<span data-ttu-id="bb379-241">中间件为压缩指定了一组默认的 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="bb379-241">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="bb379-242">用响应压缩中间件选项替换或追加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="bb379-242">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="bb379-243">请注意， `text/*`不支持通配符 MIME 类型，如不支持。</span><span class="sxs-lookup"><span data-stu-id="bb379-243">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="bb379-244">该示例应用为添加了 MIME 类型`image/svg+xml` ，并对 ASP.NET Core 横幅图像（*横幅*）进行压缩和服务。</span><span class="sxs-lookup"><span data-stu-id="bb379-244">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="bb379-245">安全协议压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-245">Compression with secure protocol</span></span>

<span data-ttu-id="bb379-246">可以使用默认情况下禁用的选项控制安全`EnableForHttps`连接上的压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-246">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="bb379-247">在动态生成的页面中使用压缩可能会导致安全问题，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="bb379-247">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="bb379-248">添加 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="bb379-248">Adding the Vary header</span></span>

<span data-ttu-id="bb379-249">当基于`Accept-Encoding`标头压缩响应时，可能会有多个压缩版本的响应和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="bb379-249">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="bb379-250">为了指示客户端和代理缓存有多个版本存在且应该存储，将使用`Vary`一个`Accept-Encoding`值添加标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-250">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="bb379-251">在 ASP.NET Core 2.0 或更高版本中，中间`Vary`件会在对响应进行压缩时自动添加标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-251">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="bb379-252">Nginx 反向代理后的中间件问题</span><span class="sxs-lookup"><span data-stu-id="bb379-252">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="bb379-253">当 Nginx 代理请求时，将删除该`Accept-Encoding`标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-253">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="bb379-254">删除`Accept-Encoding`标头会阻止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-254">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="bb379-255">有关详细信息，请参阅[NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="bb379-255">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="bb379-256">此问题通过[Nginx 的传递压缩（aspnet/BasicMiddleware #123）](https://github.com/aspnet/BasicMiddleware/issues/123)进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="bb379-256">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="bb379-257">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-257">Working with IIS dynamic compression</span></span>

<span data-ttu-id="bb379-258">如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块，请使用*web.config*文件的附加功能禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="bb379-258">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="bb379-259">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="bb379-259">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bb379-260">疑难解答</span><span class="sxs-lookup"><span data-stu-id="bb379-260">Troubleshooting</span></span>

<span data-ttu-id="bb379-261">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具，通过该工具，可以设置`Accept-Encoding`请求标头并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="bb379-261">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="bb379-262">默认情况下，响应压缩中间件会压缩满足以下条件的响应：</span><span class="sxs-lookup"><span data-stu-id="bb379-262">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="bb379-263">`Accept-Encoding`标头的值为`br`、 `gzip`、 `*`或自定义编码，它们与已建立的自定义压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="bb379-263">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="bb379-264">该值不能为`identity` ，也不能为数值（qvalue， `q`）设置为0（零）。</span><span class="sxs-lookup"><span data-stu-id="bb379-264">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="bb379-265">必须设置 MIME 类型`Content-Type`（），并且必须与在上配置的 mime 类型匹配<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>。</span><span class="sxs-lookup"><span data-stu-id="bb379-265">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="bb379-266">请求不得包含`Content-Range`标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-266">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="bb379-267">除非在响应压缩中间件选项中配置了安全协议（https），否则请求必须使用不安全的协议（http）。</span><span class="sxs-lookup"><span data-stu-id="bb379-267">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="bb379-268">*启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="bb379-268">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bb379-269">其他资源</span><span class="sxs-lookup"><span data-stu-id="bb379-269">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="bb379-270">Mozilla 开发人员网络：接受编码</span><span class="sxs-lookup"><span data-stu-id="bb379-270">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="bb379-271">RFC 7231 部分3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="bb379-271">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="bb379-272">RFC 7230 部分4.2.3： Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="bb379-272">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="bb379-273">GZIP 文件格式规范版本4。3</span><span class="sxs-lookup"><span data-stu-id="bb379-273">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="bb379-274">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="bb379-274">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="bb379-275">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="bb379-275">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="bb379-276">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-276">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="bb379-277">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="bb379-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="bb379-278">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="bb379-278">When to use Response Compression Middleware</span></span>

<span data-ttu-id="bb379-279">在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="bb379-279">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="bb379-280">中间件的性能与服务器模块的性能可能不一致。</span><span class="sxs-lookup"><span data-stu-id="bb379-280">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="bb379-281">[Http.sys 服务器](xref:fundamentals/servers/httpsys)服务器和[Kestrel](xref:fundamentals/servers/kestrel)服务器当前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="bb379-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="bb379-282">使用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="bb379-282">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="bb379-283">无法使用以下基于服务器的压缩技术：</span><span class="sxs-lookup"><span data-stu-id="bb379-283">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="bb379-284">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="bb379-284">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="bb379-285">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="bb379-285">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="bb379-286">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-286">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="bb379-287">直接在上托管：</span><span class="sxs-lookup"><span data-stu-id="bb379-287">Hosting directly on:</span></span>
  * <span data-ttu-id="bb379-288">[Http.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）</span><span class="sxs-lookup"><span data-stu-id="bb379-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="bb379-289">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="bb379-289">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="bb379-290">响应压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-290">Response compression</span></span>

<span data-ttu-id="bb379-291">通常，任何未进行本机压缩的响应都可以从响应压缩中获益。</span><span class="sxs-lookup"><span data-stu-id="bb379-291">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="bb379-292">不以本机方式压缩的响应通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="bb379-292">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="bb379-293">不应压缩本机压缩的资产，例如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="bb379-293">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="bb379-294">如果尝试进一步压缩本机压缩的响应，则可能会相形见绌处理压缩所需的时间，从而减少大小和传输时间的任何小部分。</span><span class="sxs-lookup"><span data-stu-id="bb379-294">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="bb379-295">不要压缩小于大约150-1000 字节的文件（具体取决于文件的内容和压缩的效率）。</span><span class="sxs-lookup"><span data-stu-id="bb379-295">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="bb379-296">压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="bb379-296">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="bb379-297">如果客户端可以处理压缩的内容，则客户端必须通过请求发送`Accept-Encoding`标头来通知服务器的功能。</span><span class="sxs-lookup"><span data-stu-id="bb379-297">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="bb379-298">当服务器发送压缩的内容时，它必须在`Content-Encoding`标头中包含有关如何对压缩的响应进行编码的信息。</span><span class="sxs-lookup"><span data-stu-id="bb379-298">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="bb379-299">下表显示了中间件支持的内容编码称号。</span><span class="sxs-lookup"><span data-stu-id="bb379-299">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="bb379-300">`Accept-Encoding`标头值</span><span class="sxs-lookup"><span data-stu-id="bb379-300">`Accept-Encoding` header values</span></span> | <span data-ttu-id="bb379-301">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="bb379-301">Middleware Supported</span></span> | <span data-ttu-id="bb379-302">说明</span><span class="sxs-lookup"><span data-stu-id="bb379-302">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="bb379-303">是（默认）</span><span class="sxs-lookup"><span data-stu-id="bb379-303">Yes (default)</span></span>        | [<span data-ttu-id="bb379-304">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="bb379-304">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="bb379-305">否</span><span class="sxs-lookup"><span data-stu-id="bb379-305">No</span></span>                   | [<span data-ttu-id="bb379-306">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="bb379-306">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="bb379-307">否</span><span class="sxs-lookup"><span data-stu-id="bb379-307">No</span></span>                   | [<span data-ttu-id="bb379-308">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="bb379-308">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="bb379-309">是</span><span class="sxs-lookup"><span data-stu-id="bb379-309">Yes</span></span>                  | [<span data-ttu-id="bb379-310">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="bb379-310">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="bb379-311">是</span><span class="sxs-lookup"><span data-stu-id="bb379-311">Yes</span></span>                  | <span data-ttu-id="bb379-312">"无编码" 标识符：不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="bb379-312">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="bb379-313">否</span><span class="sxs-lookup"><span data-stu-id="bb379-313">No</span></span>                   | [<span data-ttu-id="bb379-314">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="bb379-314">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="bb379-315">是</span><span class="sxs-lookup"><span data-stu-id="bb379-315">Yes</span></span>                  | <span data-ttu-id="bb379-316">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="bb379-316">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="bb379-317">有关详细信息，请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="bb379-317">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="bb379-318">中间件允许您为自定义`Accept-Encoding`标头值添加其他压缩提供程序。</span><span class="sxs-lookup"><span data-stu-id="bb379-318">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="bb379-319">有关详细信息，请参阅下面的[自定义提供程序](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="bb379-319">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="bb379-320">当客户端发送时，中间件能够对质量值`q`（qvalue，）加权作出反应，以确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="bb379-320">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="bb379-321">有关详细信息，请参阅[RFC 7231：接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="bb379-321">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="bb379-322">压缩算法会在压缩速度与压缩效率之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="bb379-322">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="bb379-323">此上下文的*有效性*是指压缩后的输出大小。</span><span class="sxs-lookup"><span data-stu-id="bb379-323">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="bb379-324">最小大小是通过*最佳*压缩来实现的。</span><span class="sxs-lookup"><span data-stu-id="bb379-324">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="bb379-325">下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-325">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="bb379-326">Header</span><span class="sxs-lookup"><span data-stu-id="bb379-326">Header</span></span>             | <span data-ttu-id="bb379-327">Role</span><span class="sxs-lookup"><span data-stu-id="bb379-327">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="bb379-328">从客户端发送到服务器，以指示客户端可接受的内容编码方案。</span><span class="sxs-lookup"><span data-stu-id="bb379-328">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="bb379-329">从服务器发送到客户端，以指示有效负载中内容的编码。</span><span class="sxs-lookup"><span data-stu-id="bb379-329">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="bb379-330">进行压缩时，将`Content-Length`删除该标头，因为在对响应进行压缩时，正文内容会发生更改。</span><span class="sxs-lookup"><span data-stu-id="bb379-330">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="bb379-331">进行压缩时，将`Content-MD5`删除该标头，因为正文内容已更改且哈希不再有效。</span><span class="sxs-lookup"><span data-stu-id="bb379-331">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="bb379-332">指定内容的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="bb379-332">Specifies the MIME type of the content.</span></span> <span data-ttu-id="bb379-333">每个响应都应`Content-Type`指定其。</span><span class="sxs-lookup"><span data-stu-id="bb379-333">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="bb379-334">中间件会检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-334">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="bb379-335">中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types)，但你可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="bb379-335">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="bb379-336">当服务器将值为的`Accept-Encoding`客户端和代理服务器发送时， `Vary`标头将向客户端或代理发送根据请求`Accept-Encoding`标头的值来缓存（变化）的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-336">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="bb379-337">将内容与`Vary: Accept-Encoding`标头一起返回的结果是：压缩的和未压缩的响应都单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="bb379-337">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="bb379-338">浏览用于[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。</span><span class="sxs-lookup"><span data-stu-id="bb379-338">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="bb379-339">该示例演示：</span><span class="sxs-lookup"><span data-stu-id="bb379-339">The sample illustrates:</span></span>

* <span data-ttu-id="bb379-340">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-340">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="bb379-341">如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-341">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="bb379-342">包</span><span class="sxs-lookup"><span data-stu-id="bb379-342">Package</span></span>

<span data-ttu-id="bb379-343">若要将中间件包含在项目中，请添加对[AspNetCore 元包](xref:fundamentals/metapackage-app)的引用，其中包括[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。</span><span class="sxs-lookup"><span data-stu-id="bb379-343">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="bb379-344">配置</span><span class="sxs-lookup"><span data-stu-id="bb379-344">Configuration</span></span>

<span data-ttu-id="bb379-345">下面的代码演示如何为默认 MIME 类型和压缩提供程序（[Brotli](#brotli-compression-provider)和[Gzip](#gzip-compression-provider)）启用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="bb379-345">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="bb379-346">注意：</span><span class="sxs-lookup"><span data-stu-id="bb379-346">Notes:</span></span>

* <span data-ttu-id="bb379-347">`app.UseResponseCompression`必须在压缩响应的任何中间件前调用。</span><span class="sxs-lookup"><span data-stu-id="bb379-347">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="bb379-348">有关详细信息，请参阅 <xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="bb379-348">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="bb379-349">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具设置`Accept-Encoding`请求标头，并检查响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="bb379-349">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="bb379-350">在没有`Accept-Encoding`标头的情况下向示例应用程序提交请求，并观察响应是否未压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-350">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="bb379-351">响应`Content-Encoding`中`Vary`不存在和标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-351">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口显示请求的结果，无需接受编码标头。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="bb379-354">将请求提交到带有`Accept-Encoding: br`标头的示例应用（Brotli 压缩），并观察响应是否已压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-354">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="bb379-355">`Content-Encoding`和`Vary`标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="bb379-355">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值为 br 的请求的结果。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="bb379-359">提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-359">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="bb379-360">Brotli 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-360">Brotli Compression Provider</span></span>

<span data-ttu-id="bb379-361"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>使用压缩[Brotli 压缩数据格式](https://tools.ietf.org/html/rfc7932)的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-361">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="bb379-362">如果没有将压缩提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：</span><span class="sxs-lookup"><span data-stu-id="bb379-362">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="bb379-363">默认情况下，Brotli 压缩提供程序会随[Gzip 压缩提供程序](#gzip-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="bb379-363">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="bb379-364">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-364">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="bb379-365">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="bb379-365">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="bb379-366">在显式添加任何压缩提供程序时，必须添加 Brotli 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="bb379-366">The Brotli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bb379-367">设置的压缩级别<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>。</span><span class="sxs-lookup"><span data-stu-id="bb379-367">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="bb379-368">Brotli 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-368">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="bb379-369">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-369">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="bb379-370">Compression Level</span><span class="sxs-lookup"><span data-stu-id="bb379-370">Compression Level</span></span> | <span data-ttu-id="bb379-371">说明</span><span class="sxs-lookup"><span data-stu-id="bb379-371">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="bb379-372">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="bb379-372">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-373">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-373">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="bb379-374">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="bb379-374">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-375">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-375">No compression should be performed.</span></span> |
| [<span data-ttu-id="bb379-376">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="bb379-376">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-377">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-377">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="bb379-378">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-378">Gzip Compression Provider</span></span>

<span data-ttu-id="bb379-379"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>使用压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-379">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="bb379-380">如果没有将压缩提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：</span><span class="sxs-lookup"><span data-stu-id="bb379-380">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="bb379-381">默认情况下，Gzip 压缩提供程序随[Brotli 压缩提供程序](#brotli-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="bb379-381">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="bb379-382">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-382">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="bb379-383">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="bb379-383">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="bb379-384">在显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="bb379-384">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="bb379-385">设置的压缩级别<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>。</span><span class="sxs-lookup"><span data-stu-id="bb379-385">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="bb379-386">Gzip 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-386">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="bb379-387">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-387">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="bb379-388">Compression Level</span><span class="sxs-lookup"><span data-stu-id="bb379-388">Compression Level</span></span> | <span data-ttu-id="bb379-389">说明</span><span class="sxs-lookup"><span data-stu-id="bb379-389">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="bb379-390">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="bb379-390">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-391">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-391">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="bb379-392">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="bb379-392">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-393">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-393">No compression should be performed.</span></span> |
| [<span data-ttu-id="bb379-394">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="bb379-394">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-395">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-395">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="bb379-396">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-396">Custom providers</span></span>

<span data-ttu-id="bb379-397">通过<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>创建自定义压缩实现。</span><span class="sxs-lookup"><span data-stu-id="bb379-397">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="bb379-398"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示此`ICompressionProvider`生成的内容编码。</span><span class="sxs-lookup"><span data-stu-id="bb379-398">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="bb379-399">中间件使用这些信息根据请求的`Accept-Encoding`标头中指定的列表来选择提供程序。</span><span class="sxs-lookup"><span data-stu-id="bb379-399">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="bb379-400">使用示例应用程序，客户端提交带有`Accept-Encoding: mycustomcompression`标头的请求。</span><span class="sxs-lookup"><span data-stu-id="bb379-400">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="bb379-401">中间件使用自定义压缩实现并返回带有`Content-Encoding: mycustomcompression`标头的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-401">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="bb379-402">客户端必须能够解压缩自定义编码，才能使自定义压缩实现正常运行。</span><span class="sxs-lookup"><span data-stu-id="bb379-402">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="bb379-403">将请求提交到带有`Accept-Encoding: mycustomcompression`标头的示例应用，并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-403">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="bb379-404">`Vary`和`Content-Encoding`标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="bb379-404">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="bb379-405">示例不压缩响应正文（未显示）。</span><span class="sxs-lookup"><span data-stu-id="bb379-405">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="bb379-406">示例的`CustomCompressionProvider`类中没有压缩实现。</span><span class="sxs-lookup"><span data-stu-id="bb379-406">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="bb379-407">但是，该示例显示了实现此类压缩算法的位置。</span><span class="sxs-lookup"><span data-stu-id="bb379-407">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="bb379-410">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="bb379-410">MIME types</span></span>

<span data-ttu-id="bb379-411">中间件为压缩指定了一组默认的 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="bb379-411">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="bb379-412">用响应压缩中间件选项替换或追加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="bb379-412">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="bb379-413">请注意， `text/*`不支持通配符 MIME 类型，如不支持。</span><span class="sxs-lookup"><span data-stu-id="bb379-413">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="bb379-414">该示例应用为添加了 MIME 类型`image/svg+xml` ，并对 ASP.NET Core 横幅图像（*横幅*）进行压缩和服务。</span><span class="sxs-lookup"><span data-stu-id="bb379-414">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="bb379-415">安全协议压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-415">Compression with secure protocol</span></span>

<span data-ttu-id="bb379-416">可以使用默认情况下禁用的选项控制安全`EnableForHttps`连接上的压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-416">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="bb379-417">在动态生成的页面中使用压缩可能会导致安全问题，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="bb379-417">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="bb379-418">添加 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="bb379-418">Adding the Vary header</span></span>

<span data-ttu-id="bb379-419">当基于`Accept-Encoding`标头压缩响应时，可能会有多个压缩版本的响应和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="bb379-419">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="bb379-420">为了指示客户端和代理缓存有多个版本存在且应该存储，将使用`Vary`一个`Accept-Encoding`值添加标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-420">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="bb379-421">在 ASP.NET Core 2.0 或更高版本中，中间`Vary`件会在对响应进行压缩时自动添加标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-421">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="bb379-422">Nginx 反向代理后的中间件问题</span><span class="sxs-lookup"><span data-stu-id="bb379-422">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="bb379-423">当 Nginx 代理请求时，将删除该`Accept-Encoding`标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-423">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="bb379-424">删除`Accept-Encoding`标头会阻止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-424">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="bb379-425">有关详细信息，请参阅[NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="bb379-425">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="bb379-426">此问题通过[Nginx 的传递压缩（aspnet/BasicMiddleware #123）](https://github.com/aspnet/BasicMiddleware/issues/123)进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="bb379-426">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="bb379-427">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-427">Working with IIS dynamic compression</span></span>

<span data-ttu-id="bb379-428">如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块，请使用*web.config*文件的附加功能禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="bb379-428">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="bb379-429">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="bb379-429">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bb379-430">疑难解答</span><span class="sxs-lookup"><span data-stu-id="bb379-430">Troubleshooting</span></span>

<span data-ttu-id="bb379-431">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具，通过该工具，可以设置`Accept-Encoding`请求标头并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="bb379-431">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="bb379-432">默认情况下，响应压缩中间件会压缩满足以下条件的响应：</span><span class="sxs-lookup"><span data-stu-id="bb379-432">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="bb379-433">`Accept-Encoding`标头的值为`br`、 `gzip`、 `*`或自定义编码，它们与已建立的自定义压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="bb379-433">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="bb379-434">该值不能为`identity` ，也不能为数值（qvalue， `q`）设置为0（零）。</span><span class="sxs-lookup"><span data-stu-id="bb379-434">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="bb379-435">必须设置 MIME 类型`Content-Type`（），并且必须与在上配置的 mime 类型匹配<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>。</span><span class="sxs-lookup"><span data-stu-id="bb379-435">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="bb379-436">请求不得包含`Content-Range`标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-436">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="bb379-437">除非在响应压缩中间件选项中配置了安全协议（https），否则请求必须使用不安全的协议（http）。</span><span class="sxs-lookup"><span data-stu-id="bb379-437">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="bb379-438">*启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="bb379-438">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bb379-439">其他资源</span><span class="sxs-lookup"><span data-stu-id="bb379-439">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="bb379-440">Mozilla 开发人员网络：接受编码</span><span class="sxs-lookup"><span data-stu-id="bb379-440">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="bb379-441">RFC 7231 部分3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="bb379-441">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="bb379-442">RFC 7230 部分4.2.3： Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="bb379-442">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="bb379-443">GZIP 文件格式规范版本4。3</span><span class="sxs-lookup"><span data-stu-id="bb379-443">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="bb379-444">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="bb379-444">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="bb379-445">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="bb379-445">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="bb379-446">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-446">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="bb379-447">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="bb379-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="bb379-448">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="bb379-448">When to use Response Compression Middleware</span></span>

<span data-ttu-id="bb379-449">在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="bb379-449">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="bb379-450">中间件的性能与服务器模块的性能可能不一致。</span><span class="sxs-lookup"><span data-stu-id="bb379-450">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="bb379-451">[Http.sys 服务器](xref:fundamentals/servers/httpsys)服务器和[Kestrel](xref:fundamentals/servers/kestrel)服务器当前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="bb379-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="bb379-452">使用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="bb379-452">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="bb379-453">无法使用以下基于服务器的压缩技术：</span><span class="sxs-lookup"><span data-stu-id="bb379-453">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="bb379-454">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="bb379-454">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="bb379-455">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="bb379-455">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="bb379-456">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-456">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="bb379-457">直接在上托管：</span><span class="sxs-lookup"><span data-stu-id="bb379-457">Hosting directly on:</span></span>
  * <span data-ttu-id="bb379-458">[Http.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）</span><span class="sxs-lookup"><span data-stu-id="bb379-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="bb379-459">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="bb379-459">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="bb379-460">响应压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-460">Response compression</span></span>

<span data-ttu-id="bb379-461">通常，任何未进行本机压缩的响应都可以从响应压缩中获益。</span><span class="sxs-lookup"><span data-stu-id="bb379-461">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="bb379-462">不以本机方式压缩的响应通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="bb379-462">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="bb379-463">不应压缩本机压缩的资产，例如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="bb379-463">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="bb379-464">如果尝试进一步压缩本机压缩的响应，则可能会相形见绌处理压缩所需的时间，从而减少大小和传输时间的任何小部分。</span><span class="sxs-lookup"><span data-stu-id="bb379-464">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="bb379-465">不要压缩小于大约150-1000 字节的文件（具体取决于文件的内容和压缩的效率）。</span><span class="sxs-lookup"><span data-stu-id="bb379-465">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="bb379-466">压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="bb379-466">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="bb379-467">如果客户端可以处理压缩的内容，则客户端必须通过请求发送`Accept-Encoding`标头来通知服务器的功能。</span><span class="sxs-lookup"><span data-stu-id="bb379-467">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="bb379-468">当服务器发送压缩的内容时，它必须在`Content-Encoding`标头中包含有关如何对压缩的响应进行编码的信息。</span><span class="sxs-lookup"><span data-stu-id="bb379-468">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="bb379-469">下表显示了中间件支持的内容编码称号。</span><span class="sxs-lookup"><span data-stu-id="bb379-469">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="bb379-470">`Accept-Encoding`标头值</span><span class="sxs-lookup"><span data-stu-id="bb379-470">`Accept-Encoding` header values</span></span> | <span data-ttu-id="bb379-471">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="bb379-471">Middleware Supported</span></span> | <span data-ttu-id="bb379-472">说明</span><span class="sxs-lookup"><span data-stu-id="bb379-472">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="bb379-473">否</span><span class="sxs-lookup"><span data-stu-id="bb379-473">No</span></span>                   | [<span data-ttu-id="bb379-474">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="bb379-474">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="bb379-475">否</span><span class="sxs-lookup"><span data-stu-id="bb379-475">No</span></span>                   | [<span data-ttu-id="bb379-476">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="bb379-476">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="bb379-477">否</span><span class="sxs-lookup"><span data-stu-id="bb379-477">No</span></span>                   | [<span data-ttu-id="bb379-478">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="bb379-478">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="bb379-479">是（默认）</span><span class="sxs-lookup"><span data-stu-id="bb379-479">Yes (default)</span></span>        | [<span data-ttu-id="bb379-480">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="bb379-480">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="bb379-481">是</span><span class="sxs-lookup"><span data-stu-id="bb379-481">Yes</span></span>                  | <span data-ttu-id="bb379-482">"无编码" 标识符：不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="bb379-482">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="bb379-483">否</span><span class="sxs-lookup"><span data-stu-id="bb379-483">No</span></span>                   | [<span data-ttu-id="bb379-484">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="bb379-484">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="bb379-485">是</span><span class="sxs-lookup"><span data-stu-id="bb379-485">Yes</span></span>                  | <span data-ttu-id="bb379-486">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="bb379-486">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="bb379-487">有关详细信息，请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="bb379-487">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="bb379-488">中间件允许您为自定义`Accept-Encoding`标头值添加其他压缩提供程序。</span><span class="sxs-lookup"><span data-stu-id="bb379-488">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="bb379-489">有关详细信息，请参阅下面的[自定义提供程序](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="bb379-489">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="bb379-490">当客户端发送时，中间件能够对质量值`q`（qvalue，）加权作出反应，以确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="bb379-490">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="bb379-491">有关详细信息，请参阅[RFC 7231：接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="bb379-491">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="bb379-492">压缩算法会在压缩速度与压缩效率之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="bb379-492">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="bb379-493">此上下文的*有效性*是指压缩后的输出大小。</span><span class="sxs-lookup"><span data-stu-id="bb379-493">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="bb379-494">最小大小是通过*最佳*压缩来实现的。</span><span class="sxs-lookup"><span data-stu-id="bb379-494">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="bb379-495">下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-495">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="bb379-496">Header</span><span class="sxs-lookup"><span data-stu-id="bb379-496">Header</span></span>             | <span data-ttu-id="bb379-497">Role</span><span class="sxs-lookup"><span data-stu-id="bb379-497">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="bb379-498">从客户端发送到服务器，以指示客户端可接受的内容编码方案。</span><span class="sxs-lookup"><span data-stu-id="bb379-498">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="bb379-499">从服务器发送到客户端，以指示有效负载中内容的编码。</span><span class="sxs-lookup"><span data-stu-id="bb379-499">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="bb379-500">进行压缩时，将`Content-Length`删除该标头，因为在对响应进行压缩时，正文内容会发生更改。</span><span class="sxs-lookup"><span data-stu-id="bb379-500">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="bb379-501">进行压缩时，将`Content-MD5`删除该标头，因为正文内容已更改且哈希不再有效。</span><span class="sxs-lookup"><span data-stu-id="bb379-501">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="bb379-502">指定内容的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="bb379-502">Specifies the MIME type of the content.</span></span> <span data-ttu-id="bb379-503">每个响应都应`Content-Type`指定其。</span><span class="sxs-lookup"><span data-stu-id="bb379-503">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="bb379-504">中间件会检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-504">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="bb379-505">中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types)，但你可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="bb379-505">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="bb379-506">当服务器将值为的`Accept-Encoding`客户端和代理服务器发送时， `Vary`标头将向客户端或代理发送根据请求`Accept-Encoding`标头的值来缓存（变化）的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-506">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="bb379-507">将内容与`Vary: Accept-Encoding`标头一起返回的结果是：压缩的和未压缩的响应都单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="bb379-507">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="bb379-508">浏览用于[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。</span><span class="sxs-lookup"><span data-stu-id="bb379-508">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="bb379-509">该示例演示：</span><span class="sxs-lookup"><span data-stu-id="bb379-509">The sample illustrates:</span></span>

* <span data-ttu-id="bb379-510">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-510">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="bb379-511">如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-511">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="bb379-512">包</span><span class="sxs-lookup"><span data-stu-id="bb379-512">Package</span></span>

<span data-ttu-id="bb379-513">若要将中间件包含在项目中，请添加对[AspNetCore 元包](xref:fundamentals/metapackage-app)的引用，其中包括[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。</span><span class="sxs-lookup"><span data-stu-id="bb379-513">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="bb379-514">配置</span><span class="sxs-lookup"><span data-stu-id="bb379-514">Configuration</span></span>

<span data-ttu-id="bb379-515">下面的代码演示如何为默认 MIME 类型和[Gzip 压缩提供程序](#gzip-compression-provider)启用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="bb379-515">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="bb379-516">注意：</span><span class="sxs-lookup"><span data-stu-id="bb379-516">Notes:</span></span>

* <span data-ttu-id="bb379-517">`app.UseResponseCompression`必须在压缩响应的任何中间件前调用。</span><span class="sxs-lookup"><span data-stu-id="bb379-517">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="bb379-518">有关详细信息，请参阅 <xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="bb379-518">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="bb379-519">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具设置`Accept-Encoding`请求标头，并检查响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="bb379-519">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="bb379-520">在没有`Accept-Encoding`标头的情况下向示例应用程序提交请求，并观察响应是否未压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-520">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="bb379-521">响应`Content-Encoding`中`Vary`不存在和标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-521">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口显示请求的结果，无需接受编码标头。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="bb379-524">将请求提交到带有`Accept-Encoding: gzip`标头的示例应用，并观察是否已压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-524">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="bb379-525">`Content-Encoding`和`Vary`标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="bb379-525">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![显示带有接受编码标头和值 gzip 的请求结果的 Fiddler 窗口。](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="bb379-529">提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-529">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="bb379-530">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-530">Gzip Compression Provider</span></span>

<span data-ttu-id="bb379-531"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>使用压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-531">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="bb379-532">如果没有将压缩提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：</span><span class="sxs-lookup"><span data-stu-id="bb379-532">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="bb379-533">默认情况下，将 Gzip 压缩提供程序添加到压缩提供程序的数组。</span><span class="sxs-lookup"><span data-stu-id="bb379-533">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="bb379-534">当客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="bb379-534">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="bb379-535">在显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="bb379-535">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="bb379-536">设置的压缩级别<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>。</span><span class="sxs-lookup"><span data-stu-id="bb379-536">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="bb379-537">Gzip 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-537">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="bb379-538">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-538">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="bb379-539">Compression Level</span><span class="sxs-lookup"><span data-stu-id="bb379-539">Compression Level</span></span> | <span data-ttu-id="bb379-540">说明</span><span class="sxs-lookup"><span data-stu-id="bb379-540">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="bb379-541">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="bb379-541">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-542">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-542">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="bb379-543">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="bb379-543">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-544">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="bb379-544">No compression should be performed.</span></span> |
| [<span data-ttu-id="bb379-545">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="bb379-545">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="bb379-546">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-546">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="bb379-547">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="bb379-547">Custom providers</span></span>

<span data-ttu-id="bb379-548">通过<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>创建自定义压缩实现。</span><span class="sxs-lookup"><span data-stu-id="bb379-548">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="bb379-549"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示此`ICompressionProvider`生成的内容编码。</span><span class="sxs-lookup"><span data-stu-id="bb379-549">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="bb379-550">中间件使用这些信息根据请求的`Accept-Encoding`标头中指定的列表来选择提供程序。</span><span class="sxs-lookup"><span data-stu-id="bb379-550">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="bb379-551">使用示例应用程序，客户端提交带有`Accept-Encoding: mycustomcompression`标头的请求。</span><span class="sxs-lookup"><span data-stu-id="bb379-551">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="bb379-552">中间件使用自定义压缩实现并返回带有`Content-Encoding: mycustomcompression`标头的响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-552">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="bb379-553">客户端必须能够解压缩自定义编码，才能使自定义压缩实现正常运行。</span><span class="sxs-lookup"><span data-stu-id="bb379-553">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="bb379-554">将请求提交到带有`Accept-Encoding: mycustomcompression`标头的示例应用，并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-554">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="bb379-555">`Vary`和`Content-Encoding`标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="bb379-555">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="bb379-556">示例不压缩响应正文（未显示）。</span><span class="sxs-lookup"><span data-stu-id="bb379-556">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="bb379-557">示例的`CustomCompressionProvider`类中没有压缩实现。</span><span class="sxs-lookup"><span data-stu-id="bb379-557">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="bb379-558">但是，该示例显示了实现此类压缩算法的位置。</span><span class="sxs-lookup"><span data-stu-id="bb379-558">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="bb379-561">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="bb379-561">MIME types</span></span>

<span data-ttu-id="bb379-562">中间件为压缩指定了一组默认的 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="bb379-562">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="bb379-563">用响应压缩中间件选项替换或追加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="bb379-563">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="bb379-564">请注意， `text/*`不支持通配符 MIME 类型，如不支持。</span><span class="sxs-lookup"><span data-stu-id="bb379-564">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="bb379-565">该示例应用为添加了 MIME 类型`image/svg+xml` ，并对 ASP.NET Core 横幅图像（*横幅*）进行压缩和服务。</span><span class="sxs-lookup"><span data-stu-id="bb379-565">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="bb379-566">安全协议压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-566">Compression with secure protocol</span></span>

<span data-ttu-id="bb379-567">可以使用默认情况下禁用的选项控制安全`EnableForHttps`连接上的压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-567">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="bb379-568">在动态生成的页面中使用压缩可能会导致安全问题，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="bb379-568">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="bb379-569">添加 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="bb379-569">Adding the Vary header</span></span>

<span data-ttu-id="bb379-570">当基于`Accept-Encoding`标头压缩响应时，可能会有多个压缩版本的响应和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="bb379-570">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="bb379-571">为了指示客户端和代理缓存有多个版本存在且应该存储，将使用`Vary`一个`Accept-Encoding`值添加标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-571">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="bb379-572">在 ASP.NET Core 2.0 或更高版本中，中间`Vary`件会在对响应进行压缩时自动添加标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-572">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="bb379-573">Nginx 反向代理后的中间件问题</span><span class="sxs-lookup"><span data-stu-id="bb379-573">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="bb379-574">当 Nginx 代理请求时，将删除该`Accept-Encoding`标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-574">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="bb379-575">删除`Accept-Encoding`标头会阻止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="bb379-575">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="bb379-576">有关详细信息，请参阅[NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="bb379-576">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="bb379-577">此问题通过[Nginx 的传递压缩（aspnet/BasicMiddleware #123）](https://github.com/aspnet/BasicMiddleware/issues/123)进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="bb379-577">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="bb379-578">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="bb379-578">Working with IIS dynamic compression</span></span>

<span data-ttu-id="bb379-579">如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块，请使用*web.config*文件的附加功能禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="bb379-579">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="bb379-580">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="bb379-580">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bb379-581">疑难解答</span><span class="sxs-lookup"><span data-stu-id="bb379-581">Troubleshooting</span></span>

<span data-ttu-id="bb379-582">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具，通过该工具，可以设置`Accept-Encoding`请求标头并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="bb379-582">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="bb379-583">默认情况下，响应压缩中间件会压缩满足以下条件的响应：</span><span class="sxs-lookup"><span data-stu-id="bb379-583">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="bb379-584">`Accept-Encoding`标头包含的值为`gzip`、 `*`或自定义编码与已建立的自定义压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="bb379-584">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="bb379-585">该值不能为`identity` ，也不能为数值（qvalue， `q`）设置为0（零）。</span><span class="sxs-lookup"><span data-stu-id="bb379-585">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="bb379-586">必须设置 MIME 类型`Content-Type`（），并且必须与在上配置的 mime 类型匹配<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>。</span><span class="sxs-lookup"><span data-stu-id="bb379-586">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="bb379-587">请求不得包含`Content-Range`标头。</span><span class="sxs-lookup"><span data-stu-id="bb379-587">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="bb379-588">除非在响应压缩中间件选项中配置了安全协议（https），否则请求必须使用不安全的协议（http）。</span><span class="sxs-lookup"><span data-stu-id="bb379-588">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="bb379-589">*启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="bb379-589">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bb379-590">其他资源</span><span class="sxs-lookup"><span data-stu-id="bb379-590">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="bb379-591">Mozilla 开发人员网络：接受编码</span><span class="sxs-lookup"><span data-stu-id="bb379-591">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="bb379-592">RFC 7231 部分3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="bb379-592">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="bb379-593">RFC 7230 部分4.2.3： Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="bb379-593">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="bb379-594">GZIP 文件格式规范版本4。3</span><span class="sxs-lookup"><span data-stu-id="bb379-594">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
