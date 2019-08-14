---
title: ASP.NET Core 中的响应压缩
author: guardrex
description: 了解如何响应压缩以及如何在 ASP.NET Core 应用中使用响应压缩中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/09/2019
uid: performance/response-compression
ms.openlocfilehash: e320e87179f9f1b9773a55c380684a3f3f712632
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993462"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="68137-103">ASP.NET Core 中的响应压缩</span><span class="sxs-lookup"><span data-stu-id="68137-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="68137-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="68137-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="68137-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="68137-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="68137-106">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="68137-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="68137-107">减小响应大小通常会显著提高应用程序的响应能力。</span><span class="sxs-lookup"><span data-stu-id="68137-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="68137-108">减少负载大小的一种方法是压缩应用的响应。</span><span class="sxs-lookup"><span data-stu-id="68137-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="68137-109">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="68137-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="68137-110">在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="68137-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="68137-111">中间件的性能与服务器模块的性能可能不一致。</span><span class="sxs-lookup"><span data-stu-id="68137-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="68137-112">[Http.sys 服务器](xref:fundamentals/servers/httpsys)服务器和[Kestrel](xref:fundamentals/servers/kestrel)服务器当前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="68137-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="68137-113">使用响应压缩中间件:</span><span class="sxs-lookup"><span data-stu-id="68137-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="68137-114">无法使用以下基于服务器的压缩技术:</span><span class="sxs-lookup"><span data-stu-id="68137-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="68137-115">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="68137-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="68137-116">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="68137-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="68137-117">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="68137-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="68137-118">直接在上托管:</span><span class="sxs-lookup"><span data-stu-id="68137-118">Hosting directly on:</span></span>
  * <span data-ttu-id="68137-119">[Http.sys 服务器](xref:fundamentals/servers/httpsys)(以前称为 WebListener)</span><span class="sxs-lookup"><span data-stu-id="68137-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="68137-120">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="68137-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="68137-121">响应压缩</span><span class="sxs-lookup"><span data-stu-id="68137-121">Response compression</span></span>

<span data-ttu-id="68137-122">通常, 任何未进行本机压缩的响应都可以从响应压缩中获益。</span><span class="sxs-lookup"><span data-stu-id="68137-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="68137-123">未本机压缩的响应通常包括:CSS、JavaScript、HTML、XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="68137-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="68137-124">不应压缩本机压缩的资产, 例如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="68137-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="68137-125">如果尝试进一步压缩本机压缩的响应, 则可能会相形见绌处理压缩所需的时间, 从而减少大小和传输时间的任何小部分。</span><span class="sxs-lookup"><span data-stu-id="68137-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="68137-126">不要压缩小于大约150-1000 字节的文件 (具体取决于文件的内容和压缩的效率)。</span><span class="sxs-lookup"><span data-stu-id="68137-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="68137-127">压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="68137-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="68137-128">如果客户端可以处理压缩的内容, 则客户端必须通过请求发送`Accept-Encoding`标头来通知服务器的功能。</span><span class="sxs-lookup"><span data-stu-id="68137-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="68137-129">当服务器发送压缩的内容时, 它必须在`Content-Encoding`标头中包含有关如何对压缩的响应进行编码的信息。</span><span class="sxs-lookup"><span data-stu-id="68137-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="68137-130">下表显示了中间件支持的内容编码称号。</span><span class="sxs-lookup"><span data-stu-id="68137-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="68137-131">`Accept-Encoding`标头值</span><span class="sxs-lookup"><span data-stu-id="68137-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="68137-132">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="68137-132">Middleware Supported</span></span> | <span data-ttu-id="68137-133">描述</span><span class="sxs-lookup"><span data-stu-id="68137-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="68137-134">是 (默认值)</span><span class="sxs-lookup"><span data-stu-id="68137-134">Yes (default)</span></span>        | [<span data-ttu-id="68137-135">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="68137-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="68137-136">No</span><span class="sxs-lookup"><span data-stu-id="68137-136">No</span></span>                   | [<span data-ttu-id="68137-137">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="68137-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="68137-138">No</span><span class="sxs-lookup"><span data-stu-id="68137-138">No</span></span>                   | [<span data-ttu-id="68137-139">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="68137-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="68137-140">是</span><span class="sxs-lookup"><span data-stu-id="68137-140">Yes</span></span>                  | [<span data-ttu-id="68137-141">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="68137-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="68137-142">是</span><span class="sxs-lookup"><span data-stu-id="68137-142">Yes</span></span>                  | <span data-ttu-id="68137-143">"无编码" 标识符:不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="68137-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="68137-144">No</span><span class="sxs-lookup"><span data-stu-id="68137-144">No</span></span>                   | [<span data-ttu-id="68137-145">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="68137-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="68137-146">是</span><span class="sxs-lookup"><span data-stu-id="68137-146">Yes</span></span>                  | <span data-ttu-id="68137-147">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="68137-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="68137-148">`Accept-Encoding`标头值</span><span class="sxs-lookup"><span data-stu-id="68137-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="68137-149">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="68137-149">Middleware Supported</span></span> | <span data-ttu-id="68137-150">描述</span><span class="sxs-lookup"><span data-stu-id="68137-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="68137-151">No</span><span class="sxs-lookup"><span data-stu-id="68137-151">No</span></span>                   | [<span data-ttu-id="68137-152">Brotli 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="68137-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="68137-153">No</span><span class="sxs-lookup"><span data-stu-id="68137-153">No</span></span>                   | [<span data-ttu-id="68137-154">DEFLATE 压缩数据格式</span><span class="sxs-lookup"><span data-stu-id="68137-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="68137-155">No</span><span class="sxs-lookup"><span data-stu-id="68137-155">No</span></span>                   | [<span data-ttu-id="68137-156">W3C 高效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="68137-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="68137-157">是 (默认值)</span><span class="sxs-lookup"><span data-stu-id="68137-157">Yes (default)</span></span>        | [<span data-ttu-id="68137-158">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="68137-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="68137-159">是</span><span class="sxs-lookup"><span data-stu-id="68137-159">Yes</span></span>                  | <span data-ttu-id="68137-160">"无编码" 标识符:不能对响应进行编码。</span><span class="sxs-lookup"><span data-stu-id="68137-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="68137-161">No</span><span class="sxs-lookup"><span data-stu-id="68137-161">No</span></span>                   | [<span data-ttu-id="68137-162">Java 存档的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="68137-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="68137-163">是</span><span class="sxs-lookup"><span data-stu-id="68137-163">Yes</span></span>                  | <span data-ttu-id="68137-164">未显式请求任何可用内容编码</span><span class="sxs-lookup"><span data-stu-id="68137-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="68137-165">有关详细信息, 请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="68137-165">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="68137-166">中间件允许您为自定义`Accept-Encoding`标头值添加其他压缩提供程序。</span><span class="sxs-lookup"><span data-stu-id="68137-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="68137-167">有关详细信息, 请参阅下面的[自定义提供程序](#custom-providers)。</span><span class="sxs-lookup"><span data-stu-id="68137-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="68137-168">当客户端发送时, 中间件能够对质量值`q`(qvalue,) 加权作出反应, 以确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="68137-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="68137-169">有关详细信息, 请[参阅 RFC 7231:接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="68137-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="68137-170">压缩算法会在压缩速度与压缩效率之间进行权衡。</span><span class="sxs-lookup"><span data-stu-id="68137-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="68137-171">此上下文的*有效性*是指压缩后的输出大小。</span><span class="sxs-lookup"><span data-stu-id="68137-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="68137-172">最小大小是通过*最佳*压缩来实现的。</span><span class="sxs-lookup"><span data-stu-id="68137-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="68137-173">下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。</span><span class="sxs-lookup"><span data-stu-id="68137-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="68137-174">Header</span><span class="sxs-lookup"><span data-stu-id="68137-174">Header</span></span>             | <span data-ttu-id="68137-175">角色</span><span class="sxs-lookup"><span data-stu-id="68137-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="68137-176">从客户端发送到服务器, 以指示客户端可接受的内容编码方案。</span><span class="sxs-lookup"><span data-stu-id="68137-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="68137-177">从服务器发送到客户端, 以指示有效负载中内容的编码。</span><span class="sxs-lookup"><span data-stu-id="68137-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="68137-178">进行压缩时, 将`Content-Length`删除该标头, 因为在对响应进行压缩时, 正文内容会发生更改。</span><span class="sxs-lookup"><span data-stu-id="68137-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="68137-179">进行压缩时, 将`Content-MD5`删除该标头, 因为正文内容已更改且哈希不再有效。</span><span class="sxs-lookup"><span data-stu-id="68137-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="68137-180">指定内容的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="68137-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="68137-181">每个响应都应`Content-Type`指定其。</span><span class="sxs-lookup"><span data-stu-id="68137-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="68137-182">中间件会检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="68137-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="68137-183">中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types), 但你可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="68137-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="68137-184">当服务器将值为的`Accept-Encoding`客户端和代理服务器发送时`Vary` , 标头将向客户端或代理发送根据请求`Accept-Encoding`标头的值来缓存 (变化) 的响应。</span><span class="sxs-lookup"><span data-stu-id="68137-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="68137-185">将内容与`Vary: Accept-Encoding`标头一起返回的结果是: 压缩的和未压缩的响应都单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="68137-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="68137-186">浏览用于[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。</span><span class="sxs-lookup"><span data-stu-id="68137-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="68137-187">该示例演示:</span><span class="sxs-lookup"><span data-stu-id="68137-187">The sample illustrates:</span></span>

* <span data-ttu-id="68137-188">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="68137-189">如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="68137-190">package</span><span class="sxs-lookup"><span data-stu-id="68137-190">Package</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68137-191">响应压缩中间件由[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包提供, 后者隐式包含在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="68137-191">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68137-192">若要将中间件包含在项目中, 请添加对[AspNetCore 元包](xref:fundamentals/metapackage-app)的引用, 其中包括[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。</span><span class="sxs-lookup"><span data-stu-id="68137-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="68137-193">配置</span><span class="sxs-lookup"><span data-stu-id="68137-193">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="68137-194">下面的代码演示如何为默认 MIME 类型和压缩提供程序 ([Brotli](#brotli-compression-provider)和[Gzip](#gzip-compression-provider)) 启用响应压缩中间件:</span><span class="sxs-lookup"><span data-stu-id="68137-194">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="68137-195">下面的代码演示如何为默认 MIME 类型和[Gzip 压缩提供程序](#gzip-compression-provider)启用响应压缩中间件:</span><span class="sxs-lookup"><span data-stu-id="68137-195">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

::: moniker-end

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

<span data-ttu-id="68137-196">注意：</span><span class="sxs-lookup"><span data-stu-id="68137-196">Notes:</span></span>

* <span data-ttu-id="68137-197">`app.UseResponseCompression` 必须在`app.UseMvc` 之前调用。</span><span class="sxs-lookup"><span data-stu-id="68137-197">`app.UseResponseCompression` must be called before `app.UseMvc`.</span></span>
* <span data-ttu-id="68137-198">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或`Accept-Encoding` [Postman](https://www.getpostman.com/)等工具设置请求标头, 并检查响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="68137-198">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="68137-199">在没有`Accept-Encoding`标头的情况下向示例应用程序提交请求, 并观察响应是否未压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-199">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="68137-200">响应中`Vary`不存在和标头。`Content-Encoding`</span><span class="sxs-lookup"><span data-stu-id="68137-200">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口显示请求的结果, 无需接受编码标头。](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="68137-203">将请求提交到带有`Accept-Encoding: br`标头的示例应用 (Brotli 压缩), 并观察响应是否已压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-203">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="68137-204">`Content-Encoding` 和`Vary`标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="68137-204">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 窗口, 其中显示带有接受编码标头和值为 br 的请求的结果。](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="68137-208">将请求提交到带有`Accept-Encoding: gzip`标头的示例应用, 并观察是否已压缩响应。</span><span class="sxs-lookup"><span data-stu-id="68137-208">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="68137-209">`Content-Encoding` 和`Vary`标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="68137-209">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![显示带有接受编码标头和值 gzip 的请求结果的 Fiddler 窗口。](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="68137-213">提供程序</span><span class="sxs-lookup"><span data-stu-id="68137-213">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="68137-214">Brotli 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="68137-214">Brotli Compression Provider</span></span>

<span data-ttu-id="68137-215">使用压缩[Brotli 压缩数据格式](https://tools.ietf.org/html/rfc7932)的响应。 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider></span><span class="sxs-lookup"><span data-stu-id="68137-215">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="68137-216">如果没有将压缩提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="68137-216">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="68137-217">默认情况下, Brotli 压缩提供程序会随[Gzip 压缩提供程序](#gzip-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="68137-217">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="68137-218">当客户端支持 Brotli 压缩数据格式时, 压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-218">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="68137-219">如果客户端不支持 Brotli, 则在客户端支持 Gzip 压缩时, 压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="68137-219">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="68137-220">在显式添加任何压缩提供程序时, 必须添加 Brotoli 压缩提供程序:</span><span class="sxs-lookup"><span data-stu-id="68137-220">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="68137-221">设置的压缩级别<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>。</span><span class="sxs-lookup"><span data-stu-id="68137-221">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="68137-222">Brotli 压缩提供程序默认为最快的压缩级别 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)), 这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-222">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="68137-223">如果需要最有效的压缩, 请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-223">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="68137-224">压缩级别</span><span class="sxs-lookup"><span data-stu-id="68137-224">Compression Level</span></span> | <span data-ttu-id="68137-225">描述</span><span class="sxs-lookup"><span data-stu-id="68137-225">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="68137-226">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="68137-226">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="68137-227">压缩应该尽快完成, 即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-227">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="68137-228">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="68137-228">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="68137-229">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-229">No compression should be performed.</span></span> |
| [<span data-ttu-id="68137-230">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="68137-230">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="68137-231">即使压缩需要更长的时间, 也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="68137-231">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

::: moniker-end

### <a name="gzip-compression-provider"></a><span data-ttu-id="68137-232">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="68137-232">Gzip Compression Provider</span></span>

<span data-ttu-id="68137-233">使用压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider></span><span class="sxs-lookup"><span data-stu-id="68137-233">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="68137-234">如果没有将压缩提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="68137-234">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="68137-235">默认情况下, Gzip 压缩提供程序随[Brotli 压缩提供程序](#brotli-compression-provider)一起添加到压缩提供程序的数组中。</span><span class="sxs-lookup"><span data-stu-id="68137-235">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="68137-236">当客户端支持 Brotli 压缩数据格式时, 压缩默认为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-236">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="68137-237">如果客户端不支持 Brotli, 则在客户端支持 Gzip 压缩时, 压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="68137-237">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="68137-238">默认情况下, 将 Gzip 压缩提供程序添加到压缩提供程序的数组。</span><span class="sxs-lookup"><span data-stu-id="68137-238">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="68137-239">当客户端支持 Gzip 压缩时, 压缩默认为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="68137-239">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="68137-240">在显式添加任何压缩提供程序时, 必须添加 Gzip 压缩提供程序:</span><span class="sxs-lookup"><span data-stu-id="68137-240">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

<span data-ttu-id="68137-241">设置的压缩级别<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>。</span><span class="sxs-lookup"><span data-stu-id="68137-241">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="68137-242">Gzip 压缩提供程序默认为最快的压缩级别 ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)), 这可能不会生成最有效的压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-242">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="68137-243">如果需要最有效的压缩, 请将中间件配置为最佳压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-243">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="68137-244">压缩级别</span><span class="sxs-lookup"><span data-stu-id="68137-244">Compression Level</span></span> | <span data-ttu-id="68137-245">描述</span><span class="sxs-lookup"><span data-stu-id="68137-245">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="68137-246">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="68137-246">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="68137-247">压缩应该尽快完成, 即使生成的输出未以最佳方式压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-247">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="68137-248">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="68137-248">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="68137-249">不应执行压缩。</span><span class="sxs-lookup"><span data-stu-id="68137-249">No compression should be performed.</span></span> |
| [<span data-ttu-id="68137-250">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="68137-250">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="68137-251">即使压缩需要更长的时间, 也应以最佳方式压缩响应。</span><span class="sxs-lookup"><span data-stu-id="68137-251">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="68137-252">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="68137-252">Custom providers</span></span>

<span data-ttu-id="68137-253">通过<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>创建自定义压缩实现。</span><span class="sxs-lookup"><span data-stu-id="68137-253">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="68137-254"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示此`ICompressionProvider`生成的内容编码。</span><span class="sxs-lookup"><span data-stu-id="68137-254">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="68137-255">中间件使用这些信息根据请求的`Accept-Encoding`标头中指定的列表来选择提供程序。</span><span class="sxs-lookup"><span data-stu-id="68137-255">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="68137-256">使用示例应用程序, 客户端提交带有`Accept-Encoding: mycustomcompression`标头的请求。</span><span class="sxs-lookup"><span data-stu-id="68137-256">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="68137-257">中间件使用自定义压缩实现并返回带有`Content-Encoding: mycustomcompression`标头的响应。</span><span class="sxs-lookup"><span data-stu-id="68137-257">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="68137-258">客户端必须能够解压缩自定义编码, 才能使自定义压缩实现正常运行。</span><span class="sxs-lookup"><span data-stu-id="68137-258">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="68137-259">将请求提交到带有`Accept-Encoding: mycustomcompression`标头的示例应用, 并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="68137-259">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="68137-260">`Vary` 和`Content-Encoding`标头出现在响应中。</span><span class="sxs-lookup"><span data-stu-id="68137-260">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="68137-261">示例不压缩响应正文 (未显示)。</span><span class="sxs-lookup"><span data-stu-id="68137-261">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="68137-262">示例的`CustomCompressionProvider`类中没有压缩实现。</span><span class="sxs-lookup"><span data-stu-id="68137-262">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="68137-263">但是, 该示例显示了实现此类压缩算法的位置。</span><span class="sxs-lookup"><span data-stu-id="68137-263">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler 窗口, 其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="68137-266">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="68137-266">MIME types</span></span>

<span data-ttu-id="68137-267">中间件为压缩指定了一组默认的 MIME 类型:</span><span class="sxs-lookup"><span data-stu-id="68137-267">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="68137-268">用响应压缩中间件选项替换或追加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="68137-268">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="68137-269">请注意, `text/*`不支持通配符 MIME 类型, 如不支持。</span><span class="sxs-lookup"><span data-stu-id="68137-269">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="68137-270">示例应用程序添加的 MIME 类型`image/svg+xml`和压缩，并提供横幅图像的 ASP.NET Core (*banner.svg*)。</span><span class="sxs-lookup"><span data-stu-id="68137-270">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="68137-271">安全协议压缩</span><span class="sxs-lookup"><span data-stu-id="68137-271">Compression with secure protocol</span></span>

<span data-ttu-id="68137-272">可以使用默认情况下禁用的选项控制安全`EnableForHttps`连接上的压缩响应。</span><span class="sxs-lookup"><span data-stu-id="68137-272">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="68137-273">在动态生成的页面中使用压缩可能会导致安全问题, 例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="68137-273">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="68137-274">添加 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="68137-274">Adding the Vary header</span></span>

<span data-ttu-id="68137-275">当基于`Accept-Encoding`标头压缩响应时, 可能会有多个压缩版本的响应和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="68137-275">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="68137-276">为了指示客户端和代理缓存有多个版本存在且应该存储, `Vary`将使用一个`Accept-Encoding`值添加标头。</span><span class="sxs-lookup"><span data-stu-id="68137-276">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="68137-277">在 ASP.NET Core 2.0 或更高版本，该中间件将添加`Vary`压缩响应时自动标头。</span><span class="sxs-lookup"><span data-stu-id="68137-277">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="68137-278">Nginx 反向代理后的中间件问题</span><span class="sxs-lookup"><span data-stu-id="68137-278">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="68137-279">当 Nginx 代理请求时, 将删除该`Accept-Encoding`标头。</span><span class="sxs-lookup"><span data-stu-id="68137-279">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="68137-280">`Accept-Encoding`删除标头会阻止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="68137-280">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="68137-281">有关详细信息，请参阅 [NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="68137-281">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="68137-282">此问题通过[Nginx (aspnet/BasicMiddleware \#123) 的传递压缩](https://github.com/aspnet/BasicMiddleware/issues/123)进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="68137-282">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="68137-283">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="68137-283">Working with IIS dynamic compression</span></span>

<span data-ttu-id="68137-284">如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块, 请使用*web.config*文件的附加功能禁用该模块。</span><span class="sxs-lookup"><span data-stu-id="68137-284">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="68137-285">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="68137-285">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="68137-286">疑难解答</span><span class="sxs-lookup"><span data-stu-id="68137-286">Troubleshooting</span></span>

<span data-ttu-id="68137-287">使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具, 通过该工具, 可以设置`Accept-Encoding`请求标头并研究响应标头、大小和正文。</span><span class="sxs-lookup"><span data-stu-id="68137-287">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="68137-288">默认情况下, 响应压缩中间件会压缩满足以下条件的响应:</span><span class="sxs-lookup"><span data-stu-id="68137-288">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="68137-289">标头的`br`值为、 `gzip`、 `*`或自定义编码, 它们与已建立的自定义压缩提供程序匹配。 `Accept-Encoding`</span><span class="sxs-lookup"><span data-stu-id="68137-289">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="68137-290">该值不能为`identity` , 也不能为数值 (qvalue, `q`) 设置为 0 (零)。</span><span class="sxs-lookup"><span data-stu-id="68137-290">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="68137-291">必须设置 mime 类型`Content-Type`(), 并且必须与在<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>上配置的 mime 类型匹配。</span><span class="sxs-lookup"><span data-stu-id="68137-291">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="68137-292">请求不得包含`Content-Range`标头。</span><span class="sxs-lookup"><span data-stu-id="68137-292">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="68137-293">除非在响应压缩中间件选项中配置了安全协议 (https), 否则请求必须使用不安全的协议 (http)。</span><span class="sxs-lookup"><span data-stu-id="68137-293">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="68137-294">*启用安全内容压缩时, 请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="68137-294">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="68137-295">标头包含的`gzip`值为、 `*`或自定义编码与已建立的自定义压缩提供程序匹配。 `Accept-Encoding`</span><span class="sxs-lookup"><span data-stu-id="68137-295">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="68137-296">该值不能为`identity` , 也不能为数值 (qvalue, `q`) 设置为 0 (零)。</span><span class="sxs-lookup"><span data-stu-id="68137-296">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="68137-297">必须设置 mime 类型`Content-Type`(), 并且必须与在<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>上配置的 mime 类型匹配。</span><span class="sxs-lookup"><span data-stu-id="68137-297">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="68137-298">请求不得包含`Content-Range`标头。</span><span class="sxs-lookup"><span data-stu-id="68137-298">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="68137-299">除非在响应压缩中间件选项中配置了安全协议 (https), 否则请求必须使用不安全的协议 (http)。</span><span class="sxs-lookup"><span data-stu-id="68137-299">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="68137-300">*启用安全内容压缩时, 请注意[上面所述](#compression-with-secure-protocol)的危险。*</span><span class="sxs-lookup"><span data-stu-id="68137-300">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="68137-301">其他资源</span><span class="sxs-lookup"><span data-stu-id="68137-301">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="68137-302">Mozilla 开发人员网络:Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="68137-302">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="68137-303">RFC 7231 部分 3.1.2.1:内容 Codings</span><span class="sxs-lookup"><span data-stu-id="68137-303">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="68137-304">RFC 7230 部分 4.2.3:Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="68137-304">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="68137-305">GZIP 文件格式规范版本4。3</span><span class="sxs-lookup"><span data-stu-id="68137-305">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)
