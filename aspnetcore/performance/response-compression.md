---
title: 在 ASP.NET Core 中的响应压缩
author: guardrex
description: 了解如何响应压缩以及如何在 ASP.NET Core 应用中使用响应压缩中间件。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: performance/response-compression
ms.openlocfilehash: e312d43fb62106f6ecb98367c29daa377bb227c9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893344"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="f6111-103">在 ASP.NET Core 中的响应压缩</span><span class="sxs-lookup"><span data-stu-id="f6111-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="f6111-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f6111-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f6111-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f6111-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f6111-106">网络带宽是一种有限的资源。</span><span class="sxs-lookup"><span data-stu-id="f6111-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="f6111-107">通常减少响应的大小通常显著增加的应用的响应能力。</span><span class="sxs-lookup"><span data-stu-id="f6111-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="f6111-108">若要减少有效负载大小的一种方法是压缩应用程序的响应。</span><span class="sxs-lookup"><span data-stu-id="f6111-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="f6111-109">何时使用响应压缩中间件</span><span class="sxs-lookup"><span data-stu-id="f6111-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="f6111-110">使用 IIS、 Apache 或 Nginx 中的基于服务器的响应压缩技术。</span><span class="sxs-lookup"><span data-stu-id="f6111-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="f6111-111">中间件的性能可能不匹配，服务器模块。</span><span class="sxs-lookup"><span data-stu-id="f6111-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="f6111-112">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)服务器和[Kestrel](xref:fundamentals/servers/kestrel) server 当前不提供内置的压缩支持。</span><span class="sxs-lookup"><span data-stu-id="f6111-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="f6111-113">如果你已，使用响应压缩中间件：</span><span class="sxs-lookup"><span data-stu-id="f6111-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="f6111-114">无法使用以下基于服务器的压缩技术：</span><span class="sxs-lookup"><span data-stu-id="f6111-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="f6111-115">IIS 动态压缩模块</span><span class="sxs-lookup"><span data-stu-id="f6111-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="f6111-116">Apache mod_deflate 模块</span><span class="sxs-lookup"><span data-stu-id="f6111-116">Apache mod_deflate module</span></span>](http://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="f6111-117">Nginx 压缩和解压缩</span><span class="sxs-lookup"><span data-stu-id="f6111-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="f6111-118">直接在上托管：</span><span class="sxs-lookup"><span data-stu-id="f6111-118">Hosting directly on:</span></span>
  * <span data-ttu-id="f6111-119">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）</span><span class="sxs-lookup"><span data-stu-id="f6111-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="f6111-120">Kestrel 服务器</span><span class="sxs-lookup"><span data-stu-id="f6111-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="f6111-121">响应压缩</span><span class="sxs-lookup"><span data-stu-id="f6111-121">Response compression</span></span>

<span data-ttu-id="f6111-122">通常情况下，可以从响应压缩受益本身不压缩任何响应。</span><span class="sxs-lookup"><span data-stu-id="f6111-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="f6111-123">响应本身不压缩通常包括：CSS、 JavaScript、 HTML、 XML 和 JSON。</span><span class="sxs-lookup"><span data-stu-id="f6111-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="f6111-124">不应压缩本机压缩的资产，如 PNG 文件。</span><span class="sxs-lookup"><span data-stu-id="f6111-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="f6111-125">如果您尝试进一步将压缩本机压缩的响应，小型进一步降低大小和传输时间将有可能屏蔽处理压缩花费的时间。</span><span class="sxs-lookup"><span data-stu-id="f6111-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="f6111-126">不压缩小于大约 150 1000年字节 （具体取决于文件的内容和压缩的效率） 的文件。</span><span class="sxs-lookup"><span data-stu-id="f6111-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="f6111-127">压缩小文件的开销可能会产生大于未压缩的文件的压缩的文件。</span><span class="sxs-lookup"><span data-stu-id="f6111-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="f6111-128">客户端时客户端可以处理压缩的内容，必须通过发送通知的功能的服务器`Accept-Encoding`与请求的标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="f6111-129">当一台服务器发送压缩的内容时，它必须包括中的信息`Content-Encoding`标头压缩的响应的编码方式。</span><span class="sxs-lookup"><span data-stu-id="f6111-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="f6111-130">下表中显示内容由中间件支持指定的编码内容。</span><span class="sxs-lookup"><span data-stu-id="f6111-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="f6111-131">`Accept-Encoding` 标头值</span><span class="sxs-lookup"><span data-stu-id="f6111-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="f6111-132">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="f6111-132">Middleware Supported</span></span> | <span data-ttu-id="f6111-133">描述</span><span class="sxs-lookup"><span data-stu-id="f6111-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="f6111-134">是 （默认值）</span><span class="sxs-lookup"><span data-stu-id="f6111-134">Yes (default)</span></span>        | [<span data-ttu-id="f6111-135">Brotli 压缩的数据格式</span><span class="sxs-lookup"><span data-stu-id="f6111-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="f6111-136">否</span><span class="sxs-lookup"><span data-stu-id="f6111-136">No</span></span>                   | [<span data-ttu-id="f6111-137">DEFLATE 压缩的数据格式</span><span class="sxs-lookup"><span data-stu-id="f6111-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="f6111-138">否</span><span class="sxs-lookup"><span data-stu-id="f6111-138">No</span></span>                   | [<span data-ttu-id="f6111-139">W3C 有效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="f6111-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="f6111-140">是</span><span class="sxs-lookup"><span data-stu-id="f6111-140">Yes</span></span>                  | [<span data-ttu-id="f6111-141">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="f6111-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="f6111-142">是</span><span class="sxs-lookup"><span data-stu-id="f6111-142">Yes</span></span>                  | <span data-ttu-id="f6111-143">"没有 encoding"的标识符：响应必须不进行编码。</span><span class="sxs-lookup"><span data-stu-id="f6111-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="f6111-144">否</span><span class="sxs-lookup"><span data-stu-id="f6111-144">No</span></span>                   | [<span data-ttu-id="f6111-145">Java 存档文件的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="f6111-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="f6111-146">是</span><span class="sxs-lookup"><span data-stu-id="f6111-146">Yes</span></span>                  | <span data-ttu-id="f6111-147">编码不显式请求任何可用内容</span><span class="sxs-lookup"><span data-stu-id="f6111-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="f6111-148">`Accept-Encoding` 标头值</span><span class="sxs-lookup"><span data-stu-id="f6111-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="f6111-149">支持的中间件</span><span class="sxs-lookup"><span data-stu-id="f6111-149">Middleware Supported</span></span> | <span data-ttu-id="f6111-150">描述</span><span class="sxs-lookup"><span data-stu-id="f6111-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="f6111-151">否</span><span class="sxs-lookup"><span data-stu-id="f6111-151">No</span></span>                   | [<span data-ttu-id="f6111-152">Brotli 压缩的数据格式</span><span class="sxs-lookup"><span data-stu-id="f6111-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="f6111-153">否</span><span class="sxs-lookup"><span data-stu-id="f6111-153">No</span></span>                   | [<span data-ttu-id="f6111-154">DEFLATE 压缩的数据格式</span><span class="sxs-lookup"><span data-stu-id="f6111-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="f6111-155">否</span><span class="sxs-lookup"><span data-stu-id="f6111-155">No</span></span>                   | [<span data-ttu-id="f6111-156">W3C 有效 XML 交换</span><span class="sxs-lookup"><span data-stu-id="f6111-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="f6111-157">是 （默认值）</span><span class="sxs-lookup"><span data-stu-id="f6111-157">Yes (default)</span></span>        | [<span data-ttu-id="f6111-158">Gzip 文件格式</span><span class="sxs-lookup"><span data-stu-id="f6111-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="f6111-159">是</span><span class="sxs-lookup"><span data-stu-id="f6111-159">Yes</span></span>                  | <span data-ttu-id="f6111-160">"没有 encoding"的标识符：响应必须不进行编码。</span><span class="sxs-lookup"><span data-stu-id="f6111-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="f6111-161">否</span><span class="sxs-lookup"><span data-stu-id="f6111-161">No</span></span>                   | [<span data-ttu-id="f6111-162">Java 存档文件的网络传输格式</span><span class="sxs-lookup"><span data-stu-id="f6111-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="f6111-163">是</span><span class="sxs-lookup"><span data-stu-id="f6111-163">Yes</span></span>                  | <span data-ttu-id="f6111-164">编码不显式请求任何可用内容</span><span class="sxs-lookup"><span data-stu-id="f6111-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="f6111-165">有关详细信息，请参阅[IANA 官方内容编码列表](http://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。</span><span class="sxs-lookup"><span data-stu-id="f6111-165">For more information, see the [IANA Official Content Coding List](http://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="f6111-166">中间件可以添加额外的压缩的自定义的提供程序`Accept-Encoding`标头值。</span><span class="sxs-lookup"><span data-stu-id="f6111-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="f6111-167">有关详细信息，请参阅[自定义提供程序](#custom-providers)下面。</span><span class="sxs-lookup"><span data-stu-id="f6111-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="f6111-168">中间件是能够对质量值作出反应 (qvalue， `q`) 权重时由客户端发送，以确定压缩方案的优先级。</span><span class="sxs-lookup"><span data-stu-id="f6111-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="f6111-169">有关详细信息，请参阅[RFC 7231:接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="f6111-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="f6111-170">压缩算法会有所压缩速度和压缩的效率之间的权衡。</span><span class="sxs-lookup"><span data-stu-id="f6111-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="f6111-171">*有效性*在此上下文中引用的输出大小在压缩之后的。</span><span class="sxs-lookup"><span data-stu-id="f6111-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="f6111-172">最小大小通过最*最佳*压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="f6111-173">在请求中所涉及的标头，发送、 缓存和接收压缩的内容是下表中所述。</span><span class="sxs-lookup"><span data-stu-id="f6111-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="f6111-174">Header</span><span class="sxs-lookup"><span data-stu-id="f6111-174">Header</span></span>             | <span data-ttu-id="f6111-175">角色</span><span class="sxs-lookup"><span data-stu-id="f6111-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="f6111-176">从客户端发送到服务器以指示编码方案可向客户端接受的内容。</span><span class="sxs-lookup"><span data-stu-id="f6111-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="f6111-177">从服务器发送到客户端以指示负载中的内容的编码。</span><span class="sxs-lookup"><span data-stu-id="f6111-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="f6111-178">压缩时，`Content-Length`删除标头，因为压缩响应的正文内容更改。</span><span class="sxs-lookup"><span data-stu-id="f6111-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="f6111-179">压缩时，`Content-MD5`删除标头，因为正文内容已更改，哈希将不再有效。</span><span class="sxs-lookup"><span data-stu-id="f6111-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="f6111-180">指定内容的 MIME 的类型。</span><span class="sxs-lookup"><span data-stu-id="f6111-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="f6111-181">每个响应应指定其`Content-Type`。</span><span class="sxs-lookup"><span data-stu-id="f6111-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="f6111-182">中间件将检查此值以确定是否应压缩响应。</span><span class="sxs-lookup"><span data-stu-id="f6111-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="f6111-183">中间件指定一组[默认 MIME 类型](#mime-types)，可以对进行编码，但您可以替换或添加 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="f6111-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="f6111-184">时，值为服务器发送`Accept-Encoding`到客户端和代理服务器，`Vary`标头指示与客户端或应缓存的代理 （有所不同） 响应值的基础`Accept-Encoding`的请求标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="f6111-185">返回的内容的结果`Vary: Accept-Encoding`标头是同时压缩和未压缩的响应将单独进行缓存。</span><span class="sxs-lookup"><span data-stu-id="f6111-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="f6111-186">探索的功能与响应压缩中间件[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)。</span><span class="sxs-lookup"><span data-stu-id="f6111-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="f6111-187">此示例演示了：</span><span class="sxs-lookup"><span data-stu-id="f6111-187">The sample illustrates:</span></span>

* <span data-ttu-id="f6111-188">使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="f6111-189">如何将 MIME 类型添加到压缩的 MIME 类型的默认列表。</span><span class="sxs-lookup"><span data-stu-id="f6111-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="f6111-190">package</span><span class="sxs-lookup"><span data-stu-id="f6111-190">Package</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f6111-191">若要在项目中包含中间件，添加对的引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，其中包括[Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。</span><span class="sxs-lookup"><span data-stu-id="f6111-191">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="f6111-192">若要在项目中包含中间件，添加对的引用[Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)，其中包括[Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。</span><span class="sxs-lookup"><span data-stu-id="f6111-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="f6111-193">若要在项目中包含中间件，添加对的引用[Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。</span><span class="sxs-lookup"><span data-stu-id="f6111-193">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="f6111-194">配置</span><span class="sxs-lookup"><span data-stu-id="f6111-194">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f6111-195">下面的代码演示如何启用响应压缩中间件默认 MIME 类型和压缩提供程序 ([Brotli](#brotli-compression-provider)并[Gzip](#gzip-compression-provider)):</span><span class="sxs-lookup"><span data-stu-id="f6111-195">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f6111-196">下面的代码演示如何启用默认 MIME 类型响应压缩中间件和[Gzip 压缩提供程序](#gzip-compression-provider):</span><span class="sxs-lookup"><span data-stu-id="f6111-196">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="f6111-197">注意：</span><span class="sxs-lookup"><span data-stu-id="f6111-197">Notes:</span></span>

* <span data-ttu-id="f6111-198">`app.UseResponseCompression` 必须在`app.UseMvc` 之前调用。</span><span class="sxs-lookup"><span data-stu-id="f6111-198">`app.UseResponseCompression` must be called before `app.UseMvc`.</span></span>
* <span data-ttu-id="f6111-199">使用一种工具，如[Fiddler](http://www.telerik.com/fiddler)， [Firebug](http://getfirebug.com/)，或[Postman](https://www.getpostman.com/)设置`Accept-Encoding`请求标头并研究响应标头、 大小和正文。</span><span class="sxs-lookup"><span data-stu-id="f6111-199">Use a tool such as [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="f6111-200">向示例应用程序而无需提交一个申请`Accept-Encoding`标头，并观察的响应是未压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-200">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="f6111-201">`Content-Encoding`和`Vary`标头不存在的响应。</span><span class="sxs-lookup"><span data-stu-id="f6111-201">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler 窗口，其中显示不含 Accept-encoding 标头请求的结果。](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f6111-204">向具有示例应用程序提交一个申请`Accept-Encoding: br`标头 （Brotli 压缩），并观察压缩响应。</span><span class="sxs-lookup"><span data-stu-id="f6111-204">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="f6111-205">`Content-Encoding`和`Vary`均存在于响应标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-205">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 窗口，其中显示 Accept-encoding 标头与请求的结果和 b 的值。](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f6111-209">向具有示例应用程序提交一个申请`Accept-Encoding: gzip`标头，并观察压缩响应。</span><span class="sxs-lookup"><span data-stu-id="f6111-209">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="f6111-210">`Content-Encoding`和`Vary`均存在于响应标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-210">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler 窗口，其中显示 Accept-encoding 标头与请求的结果，值为 gzip。](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="f6111-214">提供程序</span><span class="sxs-lookup"><span data-stu-id="f6111-214">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="f6111-215">Brotli 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="f6111-215">Brotli Compression Provider</span></span>

<span data-ttu-id="f6111-216">使用<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>若要压缩的响应[Brotli 压缩的数据格式](https://tools.ietf.org/html/rfc7932)。</span><span class="sxs-lookup"><span data-stu-id="f6111-216">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="f6111-217">如果不压缩到提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="f6111-217">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="f6111-218">Brotli 压缩提供程序默认情况下添加到与压缩提供程序的数组[Gzip 压缩提供程序](#gzip-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="f6111-218">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="f6111-219">当客户端支持 Brotli 压缩的数据格式时，则将默认压缩为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-219">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="f6111-220">如果客户端不支持 Brotli 时客户端支持 Gzip 压缩, 压缩默认设置为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="f6111-220">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="f6111-221">显式添加任何压缩提供程序时，必须添加 Brotoli 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="f6111-221">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="f6111-222">设置压缩级别与<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>。</span><span class="sxs-lookup"><span data-stu-id="f6111-222">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="f6111-223">Brotli 压缩提供程序默认为最快的压缩级别 ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel))，这可能不会产生最高效的压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-223">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="f6111-224">如果需要最高效的压缩，则配置理想的压缩的中间件。</span><span class="sxs-lookup"><span data-stu-id="f6111-224">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="f6111-225">压缩级别</span><span class="sxs-lookup"><span data-stu-id="f6111-225">Compression Level</span></span> | <span data-ttu-id="f6111-226">描述</span><span class="sxs-lookup"><span data-stu-id="f6111-226">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="f6111-227">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="f6111-227">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6111-228">即使不以最佳方式压缩生成的输出，应尽可能快地完成压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-228">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="f6111-229">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="f6111-229">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6111-230">应该在不进行压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-230">No compression should be performed.</span></span> |
| [<span data-ttu-id="f6111-231">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="f6111-231">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6111-232">响应应以最佳方式压缩，即使压缩操作将耗费更多时间来完成。</span><span class="sxs-lookup"><span data-stu-id="f6111-232">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="f6111-233">Gzip 压缩提供程序</span><span class="sxs-lookup"><span data-stu-id="f6111-233">Gzip Compression Provider</span></span>

<span data-ttu-id="f6111-234">使用<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>若要压缩的响应[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)。</span><span class="sxs-lookup"><span data-stu-id="f6111-234">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="f6111-235">如果不压缩到提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="f6111-235">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f6111-236">Gzip 压缩提供程序默认情况下添加到与压缩提供程序的数组[Brotli 压缩提供程序](#brotli-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="f6111-236">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="f6111-237">当客户端支持 Brotli 压缩的数据格式时，则将默认压缩为 Brotli 压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-237">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="f6111-238">如果客户端不支持 Brotli 时客户端支持 Gzip 压缩, 压缩默认设置为 Gzip。</span><span class="sxs-lookup"><span data-stu-id="f6111-238">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="f6111-239">默认情况下，Gzip 压缩提供程序添加到压缩提供程序的数组。</span><span class="sxs-lookup"><span data-stu-id="f6111-239">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="f6111-240">压缩默认值为 Gzip 时客户端支持 Gzip 压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-240">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="f6111-241">显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：</span><span class="sxs-lookup"><span data-stu-id="f6111-241">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

<span data-ttu-id="f6111-242">设置压缩级别与<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>。</span><span class="sxs-lookup"><span data-stu-id="f6111-242">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="f6111-243">Gzip 压缩提供程序默认为最快的压缩级别 ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel))，这可能不会产生最高效的压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-243">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="f6111-244">如果需要最高效的压缩，则配置理想的压缩的中间件。</span><span class="sxs-lookup"><span data-stu-id="f6111-244">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="f6111-245">压缩级别</span><span class="sxs-lookup"><span data-stu-id="f6111-245">Compression Level</span></span> | <span data-ttu-id="f6111-246">描述</span><span class="sxs-lookup"><span data-stu-id="f6111-246">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="f6111-247">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="f6111-247">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6111-248">即使不以最佳方式压缩生成的输出，应尽可能快地完成压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-248">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="f6111-249">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="f6111-249">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6111-250">应该在不进行压缩。</span><span class="sxs-lookup"><span data-stu-id="f6111-250">No compression should be performed.</span></span> |
| [<span data-ttu-id="f6111-251">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="f6111-251">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="f6111-252">响应应以最佳方式压缩，即使压缩操作将耗费更多时间来完成。</span><span class="sxs-lookup"><span data-stu-id="f6111-252">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="f6111-253">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="f6111-253">Custom providers</span></span>

<span data-ttu-id="f6111-254">创建自定义压缩实现<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>。</span><span class="sxs-lookup"><span data-stu-id="f6111-254">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="f6111-255"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示的内容编码此`ICompressionProvider`生成。</span><span class="sxs-lookup"><span data-stu-id="f6111-255">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="f6111-256">中间件使用此信息来选择基于列表中指定的提供程序`Accept-Encoding`的请求标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-256">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="f6111-257">使用示例应用程序，客户端提交的请求`Accept-Encoding: mycustomcompression`标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-257">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="f6111-258">中间件使用自定义压缩的实现，并返回与响应`Content-Encoding: mycustomcompression`标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-258">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="f6111-259">客户端必须能够将解压缩的自定义压缩实现以处理顺序中的自定义编码。</span><span class="sxs-lookup"><span data-stu-id="f6111-259">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f6111-260">向具有示例应用程序提交一个申请`Accept-Encoding: mycustomcompression`标头，并观察响应标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-260">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="f6111-261">`Vary`和`Content-Encoding`均存在于响应标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-261">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="f6111-262">此示例将不会压缩响应正文 （未显示）。</span><span class="sxs-lookup"><span data-stu-id="f6111-262">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="f6111-263">不压缩的实现中存在`CustomCompressionProvider`类的示例。</span><span class="sxs-lookup"><span data-stu-id="f6111-263">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="f6111-264">但是，此示例演示您可以用来实现此类的压缩算法。</span><span class="sxs-lookup"><span data-stu-id="f6111-264">However, the sample shows where you would implement such a compression algorithm.</span></span>

![显示的 Accept-encoding 标头与请求的结果，值为 mycustomcompression fiddler 窗口。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="f6111-267">MIME 类型</span><span class="sxs-lookup"><span data-stu-id="f6111-267">MIME types</span></span>

<span data-ttu-id="f6111-268">中间件指定一组默认的压缩的 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="f6111-268">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="f6111-269">替换还是追加使用响应压缩中间件的选项的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="f6111-269">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="f6111-270">请注意该通配符 MIME 类型，如`text/*`不受支持。</span><span class="sxs-lookup"><span data-stu-id="f6111-270">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="f6111-271">示例应用程序添加的 MIME 类型`image/svg+xml`和压缩，并提供横幅图像的 ASP.NET Core (*banner.svg*)。</span><span class="sxs-lookup"><span data-stu-id="f6111-271">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="f6111-272">使用安全协议的压缩</span><span class="sxs-lookup"><span data-stu-id="f6111-272">Compression with secure protocol</span></span>

<span data-ttu-id="f6111-273">可以使用控制通过安全连接的压缩的响应`EnableForHttps`选项，它默认处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="f6111-273">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="f6111-274">使用动态生成的页面压缩可能会导致安全问题如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))并[违反](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="f6111-274">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="f6111-275">添加将 Vary 标头</span><span class="sxs-lookup"><span data-stu-id="f6111-275">Adding the Vary header</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f6111-276">当压缩响应基于`Accept-Encoding`标头，有可能多个压缩的版本的响应的和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="f6111-276">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="f6111-277">若要指示客户端和代理服务器缓存，多个版本存在，并且应存储`Vary`标头添加与`Accept-Encoding`值。</span><span class="sxs-lookup"><span data-stu-id="f6111-277">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="f6111-278">在 ASP.NET Core 2.0 或更高版本，该中间件将添加`Vary`压缩响应时自动标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-278">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="f6111-279">当压缩响应基于`Accept-Encoding`标头，有可能多个压缩的版本的响应的和未压缩的版本。</span><span class="sxs-lookup"><span data-stu-id="f6111-279">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="f6111-280">若要指示客户端和代理服务器缓存，多个版本存在，并且应存储`Vary`标头添加与`Accept-Encoding`值。</span><span class="sxs-lookup"><span data-stu-id="f6111-280">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="f6111-281">在 ASP.NET Core 1.x 添加`Vary`手动完成到响应的标头：</span><span class="sxs-lookup"><span data-stu-id="f6111-281">In ASP.NET Core 1.x, adding the `Vary` header to the response is accomplished manually:</span></span>

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="f6111-282">当位于 Nginx 反向代理中间件问题</span><span class="sxs-lookup"><span data-stu-id="f6111-282">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="f6111-283">如果请求的是代理的 Nginx`Accept-Encoding`删除标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-283">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="f6111-284">删除`Accept-Encoding`标头可防止中间件压缩响应。</span><span class="sxs-lookup"><span data-stu-id="f6111-284">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="f6111-285">有关详细信息，请参阅 [NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。</span><span class="sxs-lookup"><span data-stu-id="f6111-285">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="f6111-286">此问题由跟踪[找出传递压缩 Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123)。</span><span class="sxs-lookup"><span data-stu-id="f6111-286">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="f6111-287">使用 IIS 动态压缩</span><span class="sxs-lookup"><span data-stu-id="f6111-287">Working with IIS dynamic compression</span></span>

<span data-ttu-id="f6111-288">如果您有一个活动 IIS 动态压缩模块你想要禁用的应用程序的服务器级别配置，禁用对模块进行补充*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="f6111-288">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="f6111-289">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="f6111-289">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f6111-290">疑难解答</span><span class="sxs-lookup"><span data-stu-id="f6111-290">Troubleshooting</span></span>

<span data-ttu-id="f6111-291">使用之类的工具[Fiddler](https://www.telerik.com/fiddler)， [Firebug](https://getfirebug.com/)，或[Postman](https://www.getpostman.com/)，这样便可以设置`Accept-Encoding`请求标头并研究响应标头、 大小和正文。</span><span class="sxs-lookup"><span data-stu-id="f6111-291">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="f6111-292">默认情况下，响应压缩中间件将压缩满足以下条件的响应：</span><span class="sxs-lookup"><span data-stu-id="f6111-292">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="f6111-293">`Accept-Encoding`值为标头，则`br`， `gzip`， `*`，或与已建立的自定义压缩提供程序相匹配的自定义编码。</span><span class="sxs-lookup"><span data-stu-id="f6111-293">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="f6111-294">值不能`identity`或具有一个质量值 (qvalue， `q`) 设置为 0 （零）。</span><span class="sxs-lookup"><span data-stu-id="f6111-294">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="f6111-295">MIME 类型 (`Content-Type`) 必须设置并且必须匹配在配置的 MIME 类型<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>。</span><span class="sxs-lookup"><span data-stu-id="f6111-295">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="f6111-296">请求必须包括`Content-Range`标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-296">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="f6111-297">请求必须使用不安全的协议 (http)，除非在响应压缩中间件选项中配置安全协议 (https)。</span><span class="sxs-lookup"><span data-stu-id="f6111-297">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="f6111-298">*请注意危险[上面所述](#compression-with-secure-protocol)启用安全内容压缩时。*</span><span class="sxs-lookup"><span data-stu-id="f6111-298">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="f6111-299">`Accept-Encoding`值为标头，则`gzip`， `*`，或与已建立的自定义压缩提供程序相匹配的自定义编码。</span><span class="sxs-lookup"><span data-stu-id="f6111-299">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="f6111-300">值不能`identity`或具有一个质量值 (qvalue， `q`) 设置为 0 （零）。</span><span class="sxs-lookup"><span data-stu-id="f6111-300">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="f6111-301">MIME 类型 (`Content-Type`) 必须设置并且必须匹配在配置的 MIME 类型<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>。</span><span class="sxs-lookup"><span data-stu-id="f6111-301">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="f6111-302">请求必须包括`Content-Range`标头。</span><span class="sxs-lookup"><span data-stu-id="f6111-302">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="f6111-303">请求必须使用不安全的协议 (http)，除非在响应压缩中间件选项中配置安全协议 (https)。</span><span class="sxs-lookup"><span data-stu-id="f6111-303">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="f6111-304">*请注意危险[上面所述](#compression-with-secure-protocol)启用安全内容压缩时。*</span><span class="sxs-lookup"><span data-stu-id="f6111-304">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f6111-305">其他资源</span><span class="sxs-lookup"><span data-stu-id="f6111-305">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="f6111-306">Mozilla 开发人员网络：Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="f6111-306">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="f6111-307">RFC 7231 节 3.1.2.1:内容 Codings</span><span class="sxs-lookup"><span data-stu-id="f6111-307">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="f6111-308">RFC 7230 4.2.3 节：Gzip 编码</span><span class="sxs-lookup"><span data-stu-id="f6111-308">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="f6111-309">GZIP 文件格式规范版本 4.3</span><span class="sxs-lookup"><span data-stu-id="f6111-309">GZIP file format specification version 4.3</span></span>](http://www.ietf.org/rfc/rfc1952.txt)
