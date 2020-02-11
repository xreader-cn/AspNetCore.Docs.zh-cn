---
title: ASP.NET Core 中的响应压缩
author: guardrex
description: 了解如何响应压缩以及如何在 ASP.NET Core 应用中使用响应压缩中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/response-compression
ms.openlocfilehash: d37b05edd55ac0d3910855563b819114cf815b43
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114804"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="1ff1f-103">ASP.NET Core 中的响应压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="1ff1f-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1ff1f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1ff1f-105">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-105">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="1ff1f-106">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-106">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="1ff1f-107">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-107">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="1ff1f-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1ff1f-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="1ff1f-109">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="1ff1f-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="1ff1f-110">在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="1ff1f-111">中间件的性能与服务器模块的性能可能不一致。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="1ff1f-112">[Http.sys 服务器](xref:fundamentals/servers/httpsys)服务器和[Kestrel](xref:fundamentals/servers/kestrel)服务器当前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="1ff1f-113">使用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="1ff1f-114">无法使用以下基于服务器的压缩技术：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="1ff1f-115">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="1ff1f-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="1ff1f-116">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="1ff1f-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="1ff1f-117">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="1ff1f-118">直接在上托管：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-118">Hosting directly on:</span></span>
  * <span data-ttu-id="1ff1f-119">[Http.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）</span><span class="sxs-lookup"><span data-stu-id="1ff1f-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="1ff1f-120">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="1ff1f-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="1ff1f-121">响应压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-121">Response compression</span></span>

<span data-ttu-id="1ff1f-122">通常，任何未进行本机压缩的响应都可以从响应压缩中获益。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="1ff1f-123">不以本机方式压缩的响应通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="1ff1f-124">不应压缩本机压缩的资产，例如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="1ff1f-125">如果尝试进一步压缩本机压缩的响应，则可能会相形见绌处理压缩所需的时间，从而减少大小和传输时间的任何小部分。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="1ff1f-126">不要压缩小于大约150-1000 字节的文件（具体取决于文件的内容和压缩的效率）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="1ff1f-127">压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="1ff1f-128">如果客户端可以处理压缩的内容，则客户端必须通过请求发送 `Accept-Encoding` 标头来通知服务器的功能。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="1ff1f-129">当服务器发送压缩内容时，它必须在 `Content-Encoding` 标头中包括有关如何对压缩的响应进行编码的信息。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="1ff1f-130">下表显示了中间件支持的内容编码称号。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="1ff1f-131">`Accept-Encoding` 标头值</span><span class="sxs-lookup"><span data-stu-id="1ff1f-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="1ff1f-132">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="1ff1f-132">Middleware Supported</span></span> | <span data-ttu-id="1ff1f-133">说明</span><span class="sxs-lookup"><span data-stu-id="1ff1f-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="1ff1f-134">是（默认值）</span><span class="sxs-lookup"><span data-stu-id="1ff1f-134">Yes (default)</span></span>        | [<span data-ttu-id="1ff1f-135">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="1ff1f-136">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-136">No</span></span>                   | [<span data-ttu-id="1ff1f-137">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="1ff1f-138">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-138">No</span></span>                   | [<span data-ttu-id="1ff1f-139">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="1ff1f-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="1ff1f-140">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-140">Yes</span></span>                  | [<span data-ttu-id="1ff1f-141">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="1ff1f-142">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-142">Yes</span></span>                  | <span data-ttu-id="1ff1f-143">"无编码" 标识符：不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="1ff1f-144">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-144">No</span></span>                   | [<span data-ttu-id="1ff1f-145">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="1ff1f-146">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-146">Yes</span></span>                  | <span data-ttu-id="1ff1f-147">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="1ff1f-147">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="1ff1f-148">有关详细信息，请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-148">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="1ff1f-149">中间件允许您为自定义 `Accept-Encoding` 标头值添加更多压缩提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-149">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="1ff1f-150">有关详细信息，请参阅下面的[自定义提供程序](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-150">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="1ff1f-151">当客户端发送时，中间件能够对质量值（qvalue、`q`）加权作出反应，以确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-151">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="1ff1f-152">有关详细信息，请参阅[RFC 7231：接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-152">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="1ff1f-153">压缩算法会在压缩速度与压缩效率之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-153">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="1ff1f-154">此上下文的*有效性*是指压缩后的输出大小。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-154">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="1ff1f-155">最小大小是通过*最佳*压缩来实现的。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-155">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="1ff1f-156">下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-156">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="1ff1f-157">标头</span><span class="sxs-lookup"><span data-stu-id="1ff1f-157">Header</span></span>             | <span data-ttu-id="1ff1f-158">角色</span><span class="sxs-lookup"><span data-stu-id="1ff1f-158">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="1ff1f-159">从客户端发送到服务器，以指示客户端可接受的内容编码方案。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-159">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="1ff1f-160">从服务器发送到客户端，以指示有效负载中内容的编码。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-160">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="1ff1f-161">进行压缩时，会删除 `Content-Length` 的标头，因为在对响应进行压缩时，正文内容会发生更改。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-161">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="1ff1f-162">进行压缩时，会删除 `Content-MD5` 标头，因为正文内容已更改，并且哈希不再有效。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-162">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="1ff1f-163">指定内容的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-163">Specifies the MIME type of the content.</span></span> <span data-ttu-id="1ff1f-164">每个响应都应指定其 `Content-Type`。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-164">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="1ff1f-165">中间件会检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-165">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="1ff1f-166">中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types)，但你可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-166">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="1ff1f-167">当服务器将 `Accept-Encoding` 的值发送到客户端和代理时，`Vary` 标头将根据请求的 `Accept-Encoding` 标头的值向客户端或代理指示它应缓存（变化）的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-167">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="1ff1f-168">`Vary: Accept-Encoding` 标头返回内容的结果是，压缩的和未压缩的响应都单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-168">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="1ff1f-169">浏览用于[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-169">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="1ff1f-170">该示例演示：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-170">The sample illustrates:</span></span>

* <span data-ttu-id="1ff1f-171">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-171">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="1ff1f-172">如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-172">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="1ff1f-173">包</span><span class="sxs-lookup"><span data-stu-id="1ff1f-173">Package</span></span>

<span data-ttu-id="1ff1f-174">响应压缩中间件由[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包提供，后者隐式包含在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-174">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="1ff1f-175">配置</span><span class="sxs-lookup"><span data-stu-id="1ff1f-175">Configuration</span></span>

<span data-ttu-id="1ff1f-176">下面的代码演示如何为默认 MIME 类型和压缩提供程序（[Brotli](#brotli-compression-provider)和[Gzip](#gzip-compression-provider)）启用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-176">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="1ff1f-177">注：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-177">Notes:</span></span>

* <span data-ttu-id="1ff1f-178">必须在压缩响应的任何中间件之前调用 `app.UseResponseCompression`。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-178">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="1ff1f-179">有关详细信息，请参阅 <xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-179">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="1ff1f-180">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具设置 `Accept-Encoding` 请求标头，并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-180">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="1ff1f-181">将请求提交到没有 `Accept-Encoding` 标头的示例应用，并观察响应是否未压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-181">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="1ff1f-182">响应中不存在 `Content-Encoding` 和 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-182">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口显示请求的结果，无需接受编码标头。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="1ff1f-185">将请求提交到带有 `Accept-Encoding: br` 标头的示例应用（Brotli 压缩），并观察响应是否已压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-185">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="1ff1f-186">响应中存在 `Content-Encoding` 和 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-186">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值为 br 的请求的结果。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="1ff1f-190">提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-190">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="1ff1f-191">Brotli 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-191">Brotli Compression Provider</span></span>

<span data-ttu-id="1ff1f-192">使用 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> 压缩[Brotli 压缩数据格式](https://tools.ietf.org/html/rfc7932)的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-192">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="1ff1f-193">如果未将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-193">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="1ff1f-194">默认情况下，Brotli 压缩提供程序会随[Gzip 压缩提供程序](#gzip-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-194">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="1ff1f-195">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-195">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="1ff1f-196">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-196">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="1ff1f-197">在显式添加任何压缩提供程序时，必须添加 Brotoli 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-197">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="1ff1f-198">设置 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-198">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="1ff1f-199">Brotli 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-199">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="1ff1f-200">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-200">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="1ff1f-201">Compression Level</span><span class="sxs-lookup"><span data-stu-id="1ff1f-201">Compression Level</span></span> | <span data-ttu-id="1ff1f-202">说明</span><span class="sxs-lookup"><span data-stu-id="1ff1f-202">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="1ff1f-203">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="1ff1f-203">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-204">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-204">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="1ff1f-205">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="1ff1f-205">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-206">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-206">No compression should be performed.</span></span> |
| [<span data-ttu-id="1ff1f-207">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="1ff1f-207">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-208">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-208">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="1ff1f-209">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-209">Gzip Compression Provider</span></span>

<span data-ttu-id="1ff1f-210">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-210">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="1ff1f-211">如果未将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-211">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="1ff1f-212">默认情况下，Gzip 压缩提供程序随[Brotli 压缩提供程序](#brotli-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-212">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="1ff1f-213">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-213">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="1ff1f-214">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-214">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="1ff1f-215">在显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-215">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="1ff1f-216">设置 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-216">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="1ff1f-217">Gzip 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-217">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="1ff1f-218">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-218">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="1ff1f-219">Compression Level</span><span class="sxs-lookup"><span data-stu-id="1ff1f-219">Compression Level</span></span> | <span data-ttu-id="1ff1f-220">说明</span><span class="sxs-lookup"><span data-stu-id="1ff1f-220">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="1ff1f-221">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="1ff1f-221">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-222">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-222">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="1ff1f-223">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="1ff1f-223">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-224">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-224">No compression should be performed.</span></span> |
| [<span data-ttu-id="1ff1f-225">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="1ff1f-225">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-226">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-226">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="1ff1f-227">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-227">Custom providers</span></span>

<span data-ttu-id="1ff1f-228"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>创建自定义压缩实现。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-228">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="1ff1f-229"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 表示此 `ICompressionProvider` 生成的内容编码。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-229">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="1ff1f-230">中间件使用这些信息根据请求的 `Accept-Encoding` 标头中指定的列表来选择提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-230">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="1ff1f-231">使用示例应用程序，客户端提交 `Accept-Encoding: mycustomcompression` 标头的请求。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-231">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="1ff1f-232">中间件使用自定义压缩实现并返回 `Content-Encoding: mycustomcompression` 标头的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-232">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="1ff1f-233">客户端必须能够解压缩自定义编码，才能使自定义压缩实现正常运行。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-233">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="1ff1f-234">将请求提交到带有 `Accept-Encoding: mycustomcompression` 标头的示例应用，并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-234">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="1ff1f-235">响应中存在 `Vary` 和 `Content-Encoding` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-235">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="1ff1f-236">示例不压缩响应正文（未显示）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-236">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="1ff1f-237">示例的 `CustomCompressionProvider` 类中没有压缩实现。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-237">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="1ff1f-238">但是，该示例显示了实现此类压缩算法的位置。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-238">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="1ff1f-241">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="1ff1f-241">MIME types</span></span>

<span data-ttu-id="1ff1f-242">中间件为压缩指定了一组默认的 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-242">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="1ff1f-243">用响应压缩中间件选项替换或追加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-243">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="1ff1f-244">请注意，不支持通配符 MIME 类型，如 `text/*`。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-244">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="1ff1f-245">示例应用为 `image/svg+xml` 添加了 MIME 类型，并对 ASP.NET Core 横幅图像（*横幅*）进行压缩和服务。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-245">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="1ff1f-246">安全协议压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-246">Compression with secure protocol</span></span>

<span data-ttu-id="1ff1f-247">可以使用默认情况下禁用 `EnableForHttps` 选项控制安全连接上的压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-247">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="1ff1f-248">在动态生成的页面中使用压缩可能会导致安全问题，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-248">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="1ff1f-249">添加 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="1ff1f-249">Adding the Vary header</span></span>

<span data-ttu-id="1ff1f-250">根据 `Accept-Encoding` 标头压缩响应时，可能会有多个压缩版本的响应和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-250">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="1ff1f-251">为了指示客户端和代理缓存有多个版本存在且应该存储，将使用 `Accept-Encoding` 的值添加 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-251">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="1ff1f-252">在 ASP.NET Core 2.0 或更高版本中，在对响应进行压缩时，中间件会自动添加 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-252">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="1ff1f-253">Nginx 反向代理后的中间件问题</span><span class="sxs-lookup"><span data-stu-id="1ff1f-253">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="1ff1f-254">当 Nginx 代理请求时，将删除 `Accept-Encoding` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-254">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="1ff1f-255">删除 `Accept-Encoding` 标头会阻止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-255">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="1ff1f-256">有关详细信息，请参阅[NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-256">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="1ff1f-257">此问题通过[Nginx 的传递压缩（aspnet/BasicMiddleware #123）](https://github.com/aspnet/BasicMiddleware/issues/123)进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-257">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="1ff1f-258">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-258">Working with IIS dynamic compression</span></span>

<span data-ttu-id="1ff1f-259">如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块，请使用*web.config*文件的附加功能禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-259">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="1ff1f-260">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-260">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1ff1f-261">故障排除</span><span class="sxs-lookup"><span data-stu-id="1ff1f-261">Troubleshooting</span></span>

<span data-ttu-id="1ff1f-262">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具，通过该工具，可以设置 `Accept-Encoding` 请求标头并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-262">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="1ff1f-263">默认情况下，响应压缩中间件会压缩满足以下条件的响应：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-263">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="1ff1f-264">`Accept-Encoding` 标头存在，其值为 `br`、`gzip`、`*`或自定义编码，此值与已建立的自定义压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-264">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="1ff1f-265">该值不能为 `identity`，或者其质量值（qvalue，`q`）设置为0（零）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-265">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="1ff1f-266">必须设置 MIME 类型（`Content-Type`），并且该类型必须与 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>上配置的 MIME 类型匹配。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-266">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="1ff1f-267">请求不能包含 `Content-Range` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-267">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="1ff1f-268">除非在响应压缩中间件选项中配置了安全协议（https），否则请求必须使用不安全的协议（http）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-268">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="1ff1f-269">*启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="1ff1f-269">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ff1f-270">其他资源</span><span class="sxs-lookup"><span data-stu-id="1ff1f-270">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="1ff1f-271">Mozilla 开发人员网络：接受编码</span><span class="sxs-lookup"><span data-stu-id="1ff1f-271">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="1ff1f-272">RFC 7231 部分3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="1ff1f-272">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="1ff1f-273">RFC 7230 部分4.2.3： Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="1ff1f-273">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="1ff1f-274">GZIP 文件格式规范版本4。3</span><span class="sxs-lookup"><span data-stu-id="1ff1f-274">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1ff1f-275">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-275">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="1ff1f-276">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-276">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="1ff1f-277">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-277">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="1ff1f-278">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1ff1f-278">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="1ff1f-279">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="1ff1f-279">When to use Response Compression Middleware</span></span>

<span data-ttu-id="1ff1f-280">在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-280">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="1ff1f-281">中间件的性能与服务器模块的性能可能不一致。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-281">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="1ff1f-282">[Http.sys 服务器](xref:fundamentals/servers/httpsys)服务器和[Kestrel](xref:fundamentals/servers/kestrel)服务器当前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-282">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="1ff1f-283">使用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-283">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="1ff1f-284">无法使用以下基于服务器的压缩技术：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-284">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="1ff1f-285">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="1ff1f-285">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="1ff1f-286">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="1ff1f-286">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="1ff1f-287">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-287">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="1ff1f-288">直接在上托管：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-288">Hosting directly on:</span></span>
  * <span data-ttu-id="1ff1f-289">[Http.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）</span><span class="sxs-lookup"><span data-stu-id="1ff1f-289">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="1ff1f-290">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="1ff1f-290">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="1ff1f-291">响应压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-291">Response compression</span></span>

<span data-ttu-id="1ff1f-292">通常，任何未进行本机压缩的响应都可以从响应压缩中获益。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-292">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="1ff1f-293">不以本机方式压缩的响应通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-293">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="1ff1f-294">不应压缩本机压缩的资产，例如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-294">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="1ff1f-295">如果尝试进一步压缩本机压缩的响应，则可能会相形见绌处理压缩所需的时间，从而减少大小和传输时间的任何小部分。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-295">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="1ff1f-296">不要压缩小于大约150-1000 字节的文件（具体取决于文件的内容和压缩的效率）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-296">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="1ff1f-297">压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-297">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="1ff1f-298">如果客户端可以处理压缩的内容，则客户端必须通过请求发送 `Accept-Encoding` 标头来通知服务器的功能。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-298">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="1ff1f-299">当服务器发送压缩内容时，它必须在 `Content-Encoding` 标头中包括有关如何对压缩的响应进行编码的信息。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-299">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="1ff1f-300">下表显示了中间件支持的内容编码称号。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-300">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="1ff1f-301">`Accept-Encoding` 标头值</span><span class="sxs-lookup"><span data-stu-id="1ff1f-301">`Accept-Encoding` header values</span></span> | <span data-ttu-id="1ff1f-302">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="1ff1f-302">Middleware Supported</span></span> | <span data-ttu-id="1ff1f-303">说明</span><span class="sxs-lookup"><span data-stu-id="1ff1f-303">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="1ff1f-304">是（默认值）</span><span class="sxs-lookup"><span data-stu-id="1ff1f-304">Yes (default)</span></span>        | [<span data-ttu-id="1ff1f-305">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-305">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="1ff1f-306">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-306">No</span></span>                   | [<span data-ttu-id="1ff1f-307">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-307">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="1ff1f-308">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-308">No</span></span>                   | [<span data-ttu-id="1ff1f-309">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="1ff1f-309">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="1ff1f-310">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-310">Yes</span></span>                  | [<span data-ttu-id="1ff1f-311">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-311">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="1ff1f-312">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-312">Yes</span></span>                  | <span data-ttu-id="1ff1f-313">"无编码" 标识符：不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-313">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="1ff1f-314">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-314">No</span></span>                   | [<span data-ttu-id="1ff1f-315">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-315">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="1ff1f-316">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-316">Yes</span></span>                  | <span data-ttu-id="1ff1f-317">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="1ff1f-317">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="1ff1f-318">有关详细信息，请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-318">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="1ff1f-319">中间件允许您为自定义 `Accept-Encoding` 标头值添加更多压缩提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-319">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="1ff1f-320">有关详细信息，请参阅下面的[自定义提供程序](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-320">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="1ff1f-321">当客户端发送时，中间件能够对质量值（qvalue、`q`）加权作出反应，以确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-321">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="1ff1f-322">有关详细信息，请参阅[RFC 7231：接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-322">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="1ff1f-323">压缩算法会在压缩速度与压缩效率之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-323">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="1ff1f-324">此上下文的*有效性*是指压缩后的输出大小。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-324">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="1ff1f-325">最小大小是通过*最佳*压缩来实现的。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-325">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="1ff1f-326">下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-326">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="1ff1f-327">标头</span><span class="sxs-lookup"><span data-stu-id="1ff1f-327">Header</span></span>             | <span data-ttu-id="1ff1f-328">角色</span><span class="sxs-lookup"><span data-stu-id="1ff1f-328">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="1ff1f-329">从客户端发送到服务器，以指示客户端可接受的内容编码方案。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-329">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="1ff1f-330">从服务器发送到客户端，以指示有效负载中内容的编码。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-330">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="1ff1f-331">进行压缩时，会删除 `Content-Length` 的标头，因为在对响应进行压缩时，正文内容会发生更改。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-331">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="1ff1f-332">进行压缩时，会删除 `Content-MD5` 标头，因为正文内容已更改，并且哈希不再有效。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-332">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="1ff1f-333">指定内容的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-333">Specifies the MIME type of the content.</span></span> <span data-ttu-id="1ff1f-334">每个响应都应指定其 `Content-Type`。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-334">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="1ff1f-335">中间件会检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-335">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="1ff1f-336">中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types)，但你可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-336">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="1ff1f-337">当服务器将 `Accept-Encoding` 的值发送到客户端和代理时，`Vary` 标头将根据请求的 `Accept-Encoding` 标头的值向客户端或代理指示它应缓存（变化）的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-337">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="1ff1f-338">`Vary: Accept-Encoding` 标头返回内容的结果是，压缩的和未压缩的响应都单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-338">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="1ff1f-339">浏览用于[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-339">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="1ff1f-340">该示例演示：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-340">The sample illustrates:</span></span>

* <span data-ttu-id="1ff1f-341">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-341">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="1ff1f-342">如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-342">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="1ff1f-343">包</span><span class="sxs-lookup"><span data-stu-id="1ff1f-343">Package</span></span>

<span data-ttu-id="1ff1f-344">若要将中间件包含在项目中，请添加对[AspNetCore 元包](xref:fundamentals/metapackage-app)的引用，其中包括[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-344">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="1ff1f-345">配置</span><span class="sxs-lookup"><span data-stu-id="1ff1f-345">Configuration</span></span>

<span data-ttu-id="1ff1f-346">下面的代码演示如何为默认 MIME 类型和压缩提供程序（[Brotli](#brotli-compression-provider)和[Gzip](#gzip-compression-provider)）启用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-346">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="1ff1f-347">注：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-347">Notes:</span></span>

* <span data-ttu-id="1ff1f-348">必须在压缩响应的任何中间件之前调用 `app.UseResponseCompression`。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-348">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="1ff1f-349">有关详细信息，请参阅 <xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-349">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="1ff1f-350">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具设置 `Accept-Encoding` 请求标头，并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-350">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="1ff1f-351">将请求提交到没有 `Accept-Encoding` 标头的示例应用，并观察响应是否未压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-351">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="1ff1f-352">响应中不存在 `Content-Encoding` 和 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-352">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口显示请求的结果，无需接受编码标头。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="1ff1f-355">将请求提交到带有 `Accept-Encoding: br` 标头的示例应用（Brotli 压缩），并观察响应是否已压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-355">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="1ff1f-356">响应中存在 `Content-Encoding` 和 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-356">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值为 br 的请求的结果。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="1ff1f-360">提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-360">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="1ff1f-361">Brotli 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-361">Brotli Compression Provider</span></span>

<span data-ttu-id="1ff1f-362">使用 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> 压缩[Brotli 压缩数据格式](https://tools.ietf.org/html/rfc7932)的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-362">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="1ff1f-363">如果未将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-363">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="1ff1f-364">默认情况下，Brotli 压缩提供程序会随[Gzip 压缩提供程序](#gzip-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-364">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="1ff1f-365">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-365">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="1ff1f-366">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-366">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="1ff1f-367">在显式添加任何压缩提供程序时，必须添加 Brotoli 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-367">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="1ff1f-368">设置 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-368">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="1ff1f-369">Brotli 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-369">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="1ff1f-370">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-370">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="1ff1f-371">Compression Level</span><span class="sxs-lookup"><span data-stu-id="1ff1f-371">Compression Level</span></span> | <span data-ttu-id="1ff1f-372">说明</span><span class="sxs-lookup"><span data-stu-id="1ff1f-372">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="1ff1f-373">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="1ff1f-373">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-374">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-374">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="1ff1f-375">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="1ff1f-375">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-376">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-376">No compression should be performed.</span></span> |
| [<span data-ttu-id="1ff1f-377">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="1ff1f-377">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-378">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-378">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="1ff1f-379">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-379">Gzip Compression Provider</span></span>

<span data-ttu-id="1ff1f-380">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-380">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="1ff1f-381">如果未将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-381">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="1ff1f-382">默认情况下，Gzip 压缩提供程序随[Brotli 压缩提供程序](#brotli-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-382">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="1ff1f-383">当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-383">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="1ff1f-384">如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-384">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="1ff1f-385">在显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-385">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="1ff1f-386">设置 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-386">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="1ff1f-387">Gzip 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-387">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="1ff1f-388">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-388">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="1ff1f-389">Compression Level</span><span class="sxs-lookup"><span data-stu-id="1ff1f-389">Compression Level</span></span> | <span data-ttu-id="1ff1f-390">说明</span><span class="sxs-lookup"><span data-stu-id="1ff1f-390">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="1ff1f-391">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="1ff1f-391">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-392">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-392">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="1ff1f-393">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="1ff1f-393">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-394">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-394">No compression should be performed.</span></span> |
| [<span data-ttu-id="1ff1f-395">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="1ff1f-395">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-396">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-396">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="1ff1f-397">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-397">Custom providers</span></span>

<span data-ttu-id="1ff1f-398"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>创建自定义压缩实现。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-398">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="1ff1f-399"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 表示此 `ICompressionProvider` 生成的内容编码。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-399">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="1ff1f-400">中间件使用这些信息根据请求的 `Accept-Encoding` 标头中指定的列表来选择提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-400">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="1ff1f-401">使用示例应用程序，客户端提交 `Accept-Encoding: mycustomcompression` 标头的请求。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-401">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="1ff1f-402">中间件使用自定义压缩实现并返回 `Content-Encoding: mycustomcompression` 标头的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-402">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="1ff1f-403">客户端必须能够解压缩自定义编码，才能使自定义压缩实现正常运行。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-403">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="1ff1f-404">将请求提交到带有 `Accept-Encoding: mycustomcompression` 标头的示例应用，并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-404">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="1ff1f-405">响应中存在 `Vary` 和 `Content-Encoding` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-405">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="1ff1f-406">示例不压缩响应正文（未显示）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-406">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="1ff1f-407">示例的 `CustomCompressionProvider` 类中没有压缩实现。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-407">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="1ff1f-408">但是，该示例显示了实现此类压缩算法的位置。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-408">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="1ff1f-411">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="1ff1f-411">MIME types</span></span>

<span data-ttu-id="1ff1f-412">中间件为压缩指定了一组默认的 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-412">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="1ff1f-413">用响应压缩中间件选项替换或追加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-413">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="1ff1f-414">请注意，不支持通配符 MIME 类型，如 `text/*`。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-414">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="1ff1f-415">示例应用为 `image/svg+xml` 添加了 MIME 类型，并对 ASP.NET Core 横幅图像（*横幅*）进行压缩和服务。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-415">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="1ff1f-416">安全协议压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-416">Compression with secure protocol</span></span>

<span data-ttu-id="1ff1f-417">可以使用默认情况下禁用 `EnableForHttps` 选项控制安全连接上的压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-417">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="1ff1f-418">在动态生成的页面中使用压缩可能会导致安全问题，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-418">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="1ff1f-419">添加 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="1ff1f-419">Adding the Vary header</span></span>

<span data-ttu-id="1ff1f-420">根据 `Accept-Encoding` 标头压缩响应时，可能会有多个压缩版本的响应和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-420">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="1ff1f-421">为了指示客户端和代理缓存有多个版本存在且应该存储，将使用 `Accept-Encoding` 的值添加 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-421">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="1ff1f-422">在 ASP.NET Core 2.0 或更高版本中，在对响应进行压缩时，中间件会自动添加 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-422">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="1ff1f-423">Nginx 反向代理后的中间件问题</span><span class="sxs-lookup"><span data-stu-id="1ff1f-423">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="1ff1f-424">当 Nginx 代理请求时，将删除 `Accept-Encoding` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-424">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="1ff1f-425">删除 `Accept-Encoding` 标头会阻止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-425">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="1ff1f-426">有关详细信息，请参阅[NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-426">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="1ff1f-427">此问题通过[Nginx 的传递压缩（aspnet/BasicMiddleware #123）](https://github.com/aspnet/BasicMiddleware/issues/123)进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-427">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="1ff1f-428">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-428">Working with IIS dynamic compression</span></span>

<span data-ttu-id="1ff1f-429">如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块，请使用*web.config*文件的附加功能禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-429">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="1ff1f-430">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-430">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1ff1f-431">故障排除</span><span class="sxs-lookup"><span data-stu-id="1ff1f-431">Troubleshooting</span></span>

<span data-ttu-id="1ff1f-432">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具，通过该工具，可以设置 `Accept-Encoding` 请求标头并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-432">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="1ff1f-433">默认情况下，响应压缩中间件会压缩满足以下条件的响应：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-433">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="1ff1f-434">`Accept-Encoding` 标头存在，其值为 `br`、`gzip`、`*`或自定义编码，此值与已建立的自定义压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-434">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="1ff1f-435">该值不能为 `identity`，或者其质量值（qvalue，`q`）设置为0（零）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-435">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="1ff1f-436">必须设置 MIME 类型（`Content-Type`），并且该类型必须与 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>上配置的 MIME 类型匹配。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-436">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="1ff1f-437">请求不能包含 `Content-Range` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-437">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="1ff1f-438">除非在响应压缩中间件选项中配置了安全协议（https），否则请求必须使用不安全的协议（http）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-438">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="1ff1f-439">*启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="1ff1f-439">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ff1f-440">其他资源</span><span class="sxs-lookup"><span data-stu-id="1ff1f-440">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="1ff1f-441">Mozilla 开发人员网络：接受编码</span><span class="sxs-lookup"><span data-stu-id="1ff1f-441">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="1ff1f-442">RFC 7231 部分3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="1ff1f-442">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="1ff1f-443">RFC 7230 部分4.2.3： Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="1ff1f-443">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="1ff1f-444">GZIP 文件格式规范版本4。3</span><span class="sxs-lookup"><span data-stu-id="1ff1f-444">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1ff1f-445">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-445">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="1ff1f-446">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-446">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="1ff1f-447">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-447">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="1ff1f-448">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1ff1f-448">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="1ff1f-449">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="1ff1f-449">When to use Response Compression Middleware</span></span>

<span data-ttu-id="1ff1f-450">在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-450">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="1ff1f-451">中间件的性能与服务器模块的性能可能不一致。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-451">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="1ff1f-452">[Http.sys 服务器](xref:fundamentals/servers/httpsys)服务器和[Kestrel](xref:fundamentals/servers/kestrel)服务器当前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-452">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="1ff1f-453">使用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-453">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="1ff1f-454">无法使用以下基于服务器的压缩技术：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-454">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="1ff1f-455">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="1ff1f-455">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="1ff1f-456">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="1ff1f-456">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="1ff1f-457">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-457">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="1ff1f-458">直接在上托管：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-458">Hosting directly on:</span></span>
  * <span data-ttu-id="1ff1f-459">[Http.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）</span><span class="sxs-lookup"><span data-stu-id="1ff1f-459">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="1ff1f-460">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="1ff1f-460">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="1ff1f-461">响应压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-461">Response compression</span></span>

<span data-ttu-id="1ff1f-462">通常，任何未进行本机压缩的响应都可以从响应压缩中获益。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-462">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="1ff1f-463">不以本机方式压缩的响应通常包括： CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-463">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="1ff1f-464">不应压缩本机压缩的资产，例如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-464">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="1ff1f-465">如果尝试进一步压缩本机压缩的响应，则可能会相形见绌处理压缩所需的时间，从而减少大小和传输时间的任何小部分。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-465">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="1ff1f-466">不要压缩小于大约150-1000 字节的文件（具体取决于文件的内容和压缩的效率）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-466">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="1ff1f-467">压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-467">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="1ff1f-468">如果客户端可以处理压缩的内容，则客户端必须通过请求发送 `Accept-Encoding` 标头来通知服务器的功能。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-468">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="1ff1f-469">当服务器发送压缩内容时，它必须在 `Content-Encoding` 标头中包括有关如何对压缩的响应进行编码的信息。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-469">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="1ff1f-470">下表显示了中间件支持的内容编码称号。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-470">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="1ff1f-471">`Accept-Encoding` 标头值</span><span class="sxs-lookup"><span data-stu-id="1ff1f-471">`Accept-Encoding` header values</span></span> | <span data-ttu-id="1ff1f-472">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="1ff1f-472">Middleware Supported</span></span> | <span data-ttu-id="1ff1f-473">说明</span><span class="sxs-lookup"><span data-stu-id="1ff1f-473">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="1ff1f-474">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-474">No</span></span>                   | [<span data-ttu-id="1ff1f-475">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-475">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="1ff1f-476">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-476">No</span></span>                   | [<span data-ttu-id="1ff1f-477">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-477">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="1ff1f-478">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-478">No</span></span>                   | [<span data-ttu-id="1ff1f-479">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="1ff1f-479">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="1ff1f-480">是（默认值）</span><span class="sxs-lookup"><span data-stu-id="1ff1f-480">Yes (default)</span></span>        | [<span data-ttu-id="1ff1f-481">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-481">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="1ff1f-482">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-482">Yes</span></span>                  | <span data-ttu-id="1ff1f-483">"无编码" 标识符：不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-483">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="1ff1f-484">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-484">No</span></span>                   | [<span data-ttu-id="1ff1f-485">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="1ff1f-485">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="1ff1f-486">是</span><span class="sxs-lookup"><span data-stu-id="1ff1f-486">Yes</span></span>                  | <span data-ttu-id="1ff1f-487">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="1ff1f-487">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="1ff1f-488">有关详细信息，请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-488">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="1ff1f-489">中间件允许您为自定义 `Accept-Encoding` 标头值添加更多压缩提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-489">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="1ff1f-490">有关详细信息，请参阅下面的[自定义提供程序](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-490">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="1ff1f-491">当客户端发送时，中间件能够对质量值（qvalue、`q`）加权作出反应，以确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-491">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="1ff1f-492">有关详细信息，请参阅[RFC 7231：接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-492">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="1ff1f-493">压缩算法会在压缩速度与压缩效率之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-493">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="1ff1f-494">此上下文的*有效性*是指压缩后的输出大小。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-494">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="1ff1f-495">最小大小是通过*最佳*压缩来实现的。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-495">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="1ff1f-496">下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-496">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="1ff1f-497">标头</span><span class="sxs-lookup"><span data-stu-id="1ff1f-497">Header</span></span>             | <span data-ttu-id="1ff1f-498">角色</span><span class="sxs-lookup"><span data-stu-id="1ff1f-498">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="1ff1f-499">从客户端发送到服务器，以指示客户端可接受的内容编码方案。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-499">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="1ff1f-500">从服务器发送到客户端，以指示有效负载中内容的编码。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-500">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="1ff1f-501">进行压缩时，会删除 `Content-Length` 的标头，因为在对响应进行压缩时，正文内容会发生更改。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-501">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="1ff1f-502">进行压缩时，会删除 `Content-MD5` 标头，因为正文内容已更改，并且哈希不再有效。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-502">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="1ff1f-503">指定内容的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-503">Specifies the MIME type of the content.</span></span> <span data-ttu-id="1ff1f-504">每个响应都应指定其 `Content-Type`。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-504">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="1ff1f-505">中间件会检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-505">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="1ff1f-506">中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types)，但你可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-506">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="1ff1f-507">当服务器将 `Accept-Encoding` 的值发送到客户端和代理时，`Vary` 标头将根据请求的 `Accept-Encoding` 标头的值向客户端或代理指示它应缓存（变化）的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-507">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="1ff1f-508">`Vary: Accept-Encoding` 标头返回内容的结果是，压缩的和未压缩的响应都单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-508">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="1ff1f-509">浏览用于[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-509">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="1ff1f-510">该示例演示：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-510">The sample illustrates:</span></span>

* <span data-ttu-id="1ff1f-511">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-511">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="1ff1f-512">如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-512">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="1ff1f-513">包</span><span class="sxs-lookup"><span data-stu-id="1ff1f-513">Package</span></span>

<span data-ttu-id="1ff1f-514">若要将中间件包含在项目中，请添加对[AspNetCore 元包](xref:fundamentals/metapackage-app)的引用，其中包括[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-514">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="1ff1f-515">配置</span><span class="sxs-lookup"><span data-stu-id="1ff1f-515">Configuration</span></span>

<span data-ttu-id="1ff1f-516">下面的代码演示如何为默认 MIME 类型和[Gzip 压缩提供程序](#gzip-compression-provider)启用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-516">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="1ff1f-517">注：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-517">Notes:</span></span>

* <span data-ttu-id="1ff1f-518">必须在压缩响应的任何中间件之前调用 `app.UseResponseCompression`。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-518">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="1ff1f-519">有关详细信息，请参阅 <xref:fundamentals/middleware/index#middleware-order>。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-519">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="1ff1f-520">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具设置 `Accept-Encoding` 请求标头，并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-520">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="1ff1f-521">将请求提交到没有 `Accept-Encoding` 标头的示例应用，并观察响应是否未压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-521">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="1ff1f-522">响应中不存在 `Content-Encoding` 和 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-522">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口显示请求的结果，无需接受编码标头。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="1ff1f-525">将请求提交到带有 `Accept-Encoding: gzip` 标头的示例应用，并观察响应是否已压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-525">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="1ff1f-526">响应中存在 `Content-Encoding` 和 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-526">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![显示带有接受编码标头和值 gzip 的请求结果的 Fiddler 窗口。](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="1ff1f-530">提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-530">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="1ff1f-531">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-531">Gzip Compression Provider</span></span>

<span data-ttu-id="1ff1f-532">使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-532">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="1ff1f-533">如果未将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-533">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="1ff1f-534">默认情况下，将 Gzip 压缩提供程序添加到压缩提供程序的数组。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-534">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="1ff1f-535">当客户端支持 Gzip 压缩时，压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-535">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="1ff1f-536">在显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-536">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="1ff1f-537">设置 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>的压缩级别。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-537">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="1ff1f-538">Gzip 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-538">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="1ff1f-539">如果需要最有效的压缩，请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-539">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="1ff1f-540">Compression Level</span><span class="sxs-lookup"><span data-stu-id="1ff1f-540">Compression Level</span></span> | <span data-ttu-id="1ff1f-541">说明</span><span class="sxs-lookup"><span data-stu-id="1ff1f-541">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="1ff1f-542">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="1ff1f-542">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-543">压缩应该尽快完成，即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-543">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="1ff1f-544">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="1ff1f-544">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-545">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-545">No compression should be performed.</span></span> |
| [<span data-ttu-id="1ff1f-546">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="1ff1f-546">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="1ff1f-547">即使压缩需要更长的时间，也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-547">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="1ff1f-548">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="1ff1f-548">Custom providers</span></span>

<span data-ttu-id="1ff1f-549"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>创建自定义压缩实现。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-549">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="1ff1f-550"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 表示此 `ICompressionProvider` 生成的内容编码。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-550">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="1ff1f-551">中间件使用这些信息根据请求的 `Accept-Encoding` 标头中指定的列表来选择提供程序。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-551">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="1ff1f-552">使用示例应用程序，客户端提交 `Accept-Encoding: mycustomcompression` 标头的请求。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-552">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="1ff1f-553">中间件使用自定义压缩实现并返回 `Content-Encoding: mycustomcompression` 标头的响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-553">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="1ff1f-554">客户端必须能够解压缩自定义编码，才能使自定义压缩实现正常运行。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-554">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="1ff1f-555">将请求提交到带有 `Accept-Encoding: mycustomcompression` 标头的示例应用，并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-555">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="1ff1f-556">响应中存在 `Vary` 和 `Content-Encoding` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-556">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="1ff1f-557">示例不压缩响应正文（未显示）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-557">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="1ff1f-558">示例的 `CustomCompressionProvider` 类中没有压缩实现。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-558">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="1ff1f-559">但是，该示例显示了实现此类压缩算法的位置。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-559">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 窗口，其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="1ff1f-562">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="1ff1f-562">MIME types</span></span>

<span data-ttu-id="1ff1f-563">中间件为压缩指定了一组默认的 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-563">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="1ff1f-564">用响应压缩中间件选项替换或追加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-564">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="1ff1f-565">请注意，不支持通配符 MIME 类型，如 `text/*`。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-565">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="1ff1f-566">示例应用为 `image/svg+xml` 添加了 MIME 类型，并对 ASP.NET Core 横幅图像（*横幅*）进行压缩和服务。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-566">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="1ff1f-567">安全协议压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-567">Compression with secure protocol</span></span>

<span data-ttu-id="1ff1f-568">可以使用默认情况下禁用 `EnableForHttps` 选项控制安全连接上的压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-568">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="1ff1f-569">在动态生成的页面中使用压缩可能会导致安全问题，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-569">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="1ff1f-570">添加 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="1ff1f-570">Adding the Vary header</span></span>

<span data-ttu-id="1ff1f-571">根据 `Accept-Encoding` 标头压缩响应时，可能会有多个压缩版本的响应和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-571">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="1ff1f-572">为了指示客户端和代理缓存有多个版本存在且应该存储，将使用 `Accept-Encoding` 的值添加 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-572">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="1ff1f-573">在 ASP.NET Core 2.0 或更高版本中，在对响应进行压缩时，中间件会自动添加 `Vary` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-573">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="1ff1f-574">Nginx 反向代理后的中间件问题</span><span class="sxs-lookup"><span data-stu-id="1ff1f-574">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="1ff1f-575">当 Nginx 代理请求时，将删除 `Accept-Encoding` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-575">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="1ff1f-576">删除 `Accept-Encoding` 标头会阻止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-576">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="1ff1f-577">有关详细信息，请参阅[NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-577">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="1ff1f-578">此问题通过[Nginx 的传递压缩（aspnet/BasicMiddleware #123）](https://github.com/aspnet/BasicMiddleware/issues/123)进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-578">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="1ff1f-579">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="1ff1f-579">Working with IIS dynamic compression</span></span>

<span data-ttu-id="1ff1f-580">如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块，请使用*web.config*文件的附加功能禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-580">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="1ff1f-581">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-581">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1ff1f-582">故障排除</span><span class="sxs-lookup"><span data-stu-id="1ff1f-582">Troubleshooting</span></span>

<span data-ttu-id="1ff1f-583">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具，通过该工具，可以设置 `Accept-Encoding` 请求标头并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-583">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="1ff1f-584">默认情况下，响应压缩中间件会压缩满足以下条件的响应：</span><span class="sxs-lookup"><span data-stu-id="1ff1f-584">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="1ff1f-585">`Accept-Encoding` 标头存在，其值为 `gzip`、`*`或自定义编码，此值与已建立的自定义压缩提供程序匹配。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-585">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="1ff1f-586">该值不能为 `identity`，或者其质量值（qvalue，`q`）设置为0（零）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-586">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="1ff1f-587">必须设置 MIME 类型（`Content-Type`），并且该类型必须与 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>上配置的 MIME 类型匹配。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-587">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="1ff1f-588">请求不能包含 `Content-Range` 标头。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-588">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="1ff1f-589">除非在响应压缩中间件选项中配置了安全协议（https），否则请求必须使用不安全的协议（http）。</span><span class="sxs-lookup"><span data-stu-id="1ff1f-589">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="1ff1f-590">*启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="1ff1f-590">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ff1f-591">其他资源</span><span class="sxs-lookup"><span data-stu-id="1ff1f-591">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="1ff1f-592">Mozilla 开发人员网络：接受编码</span><span class="sxs-lookup"><span data-stu-id="1ff1f-592">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="1ff1f-593">RFC 7231 部分3.1.2.1： Content Codings</span><span class="sxs-lookup"><span data-stu-id="1ff1f-593">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="1ff1f-594">RFC 7230 部分4.2.3： Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="1ff1f-594">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="1ff1f-595">GZIP 文件格式规范版本4。3</span><span class="sxs-lookup"><span data-stu-id="1ff1f-595">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
