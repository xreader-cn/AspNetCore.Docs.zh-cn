---
title: ASP.NET Core 中的响应压缩
author: guardrex
description: 了解如何响应压缩以及如何在 ASP.NET Core 应用中使用响应压缩中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
uid: performance/response-compression
ms.openlocfilehash: b8a84418a3258e9ac43b4eadd8564c0708590bce
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726959"
---
# <a name="response-compression-in-aspnet-core"></a>ASP.NET Core 中的响应压缩

作者：[Luke Latham](https://github.com/guardrex)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）

网络带宽是一种有限的资源。 减小响应大小通常会显著提高应用程序的响应能力。 减少负载大小的一种方法是压缩应用的响应。

## <a name="when-to-use-response-compression-middleware"></a>何时使用响应压缩中间件

在 IIS、Apache 或 Nginx 中使用基于服务器的响应压缩技术。 中间件的性能与服务器模块的性能可能不一致。 [Http.sys 服务器](xref:fundamentals/servers/httpsys)服务器和[Kestrel](xref:fundamentals/servers/kestrel)服务器当前不提供内置的压缩支持。

使用响应压缩中间件：

* 无法使用以下基于服务器的压缩技术：
  * [IIS 动态压缩模块](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate 模块](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx 压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 直接在上托管：
  * [Http.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）
  * [Kestrel 服务器](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>响应压缩

通常，任何未进行本机压缩的响应都可以从响应压缩中获益。 不以本机方式压缩的响应通常包括： CSS、JavaScript、HTML、XML 和 JSON。 不应压缩本机压缩的资产，例如 PNG 文件。 如果尝试进一步压缩本机压缩的响应，则可能会相形见绌处理压缩所需的时间，从而减少大小和传输时间的任何小部分。 不要压缩小于大约150-1000 字节的文件（具体取决于文件的内容和压缩的效率）。 压缩小文件的开销可能会产生比未压缩文件更大的压缩文件。

如果客户端可以处理压缩的内容，则客户端必须通过请求发送 `Accept-Encoding` 标头来通知服务器的功能。 当服务器发送压缩内容时，它必须在 `Content-Encoding` 标头中包括有关如何对压缩的响应进行编码的信息。 下表显示了中间件支持的内容编码称号。

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding` 标头值 | 支持的中间件 | 说明 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 是（默认值）        | [Brotli 压缩数据格式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | 是                   | [DEFLATE 压缩数据格式](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | 是                   | [W3C 高效 XML 交换](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 是                  | [Gzip 文件格式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | 是                  | "无编码" 标识符：不能对响应进行编码。 |
| `pack200-gzip`                  | 是                   | [Java 存档的网络传输格式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | 是                  | 未显式请求任何可用内容编码 |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding` 标头值 | 支持的中间件 | 说明 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 是                   | [Brotli 压缩数据格式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | 是                   | [DEFLATE 压缩数据格式](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | 是                   | [W3C 高效 XML 交换](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 是（默认值）        | [Gzip 文件格式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | 是                  | "无编码" 标识符：不能对响应进行编码。 |
| `pack200-gzip`                  | 是                   | [Java 存档的网络传输格式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | 是                  | 未显式请求任何可用内容编码 |

::: moniker-end

有关详细信息，请参阅[IANA 官方内容编码列表](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。

中间件允许您为自定义 `Accept-Encoding` 标头值添加更多压缩提供程序。 有关详细信息，请参阅下面的[自定义提供程序](#custom-providers)。

当客户端发送时，中间件能够对质量值（qvalue、`q`）加权作出反应，以确定压缩方案的优先级。 有关详细信息，请参阅[RFC 7231：接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。

压缩算法会在压缩速度与压缩效率之间进行权衡。 此上下文的*有效性*是指压缩后的输出大小。 最小大小是通过*最佳*压缩来实现的。

下表介绍了请求、发送、缓存和接收压缩内容所涉及的标头。

| 标头             | 角色 |
| ------------------ | ---- |
| `Accept-Encoding`  | 从客户端发送到服务器，以指示客户端可接受的内容编码方案。 |
| `Content-Encoding` | 从服务器发送到客户端，以指示有效负载中内容的编码。 |
| `Content-Length`   | 进行压缩时，会删除 `Content-Length` 的标头，因为在对响应进行压缩时，正文内容会发生更改。 |
| `Content-MD5`      | 进行压缩时，会删除 `Content-MD5` 标头，因为正文内容已更改，并且哈希不再有效。 |
| `Content-Type`     | 指定内容的 MIME 类型。 每个响应都应指定其 `Content-Type`。 中间件会检查此值以确定是否应压缩响应。 中间件指定了一组可进行编码的[默认 MIME 类型](#mime-types)，但你可以替换或添加 MIME 类型。 |
| `Vary`             | 当服务器将 `Accept-Encoding` 的值发送到客户端和代理时，`Vary` 标头将根据请求的 `Accept-Encoding` 标头的值向客户端或代理指示它应缓存（变化）的响应。 `Vary: Accept-Encoding` 标头返回内容的结果是，压缩的和未压缩的响应都单独进行缓存。 |

浏览用于[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)的响应压缩中间件功能。 该示例演示：

* 使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。
* 如何将 MIME 类型添加到 MIME 类型的默认列表以进行压缩。

## <a name="package"></a>包

::: moniker range=">= aspnetcore-3.0"

响应压缩中间件由[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包提供，后者隐式包含在 ASP.NET Core 应用中。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

若要将中间件包含在项目中，请添加对[AspNetCore 元包](xref:fundamentals/metapackage-app)的引用，其中包括[AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。

::: moniker-end

## <a name="configuration"></a>配置

::: moniker range=">= aspnetcore-2.2"

下面的代码演示如何为默认 MIME 类型和压缩提供程序（[Brotli](#brotli-compression-provider)和[Gzip](#gzip-compression-provider)）启用响应压缩中间件：

::: moniker-end

::: moniker range="< aspnetcore-2.2"

下面的代码演示如何为默认 MIME 类型和[Gzip 压缩提供程序](#gzip-compression-provider)启用响应压缩中间件：

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

注：

* 必须在压缩响应的任何中间件之前调用 `app.UseResponseCompression`。 有关详细信息，请参阅 <xref:fundamentals/middleware/index#middleware-order>。
* 使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)等工具设置 `Accept-Encoding` 请求标头，并研究响应标头、大小和正文。

将请求提交到没有 `Accept-Encoding` 标头的示例应用，并观察响应是否未压缩。 响应中不存在 `Content-Encoding` 和 `Vary` 标头。

![Fiddler 窗口显示请求的结果，无需接受编码标头。 响应未压缩。](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

将请求提交到带有 `Accept-Encoding: br` 标头的示例应用（Brotli 压缩），并观察响应是否已压缩。 响应中存在 `Content-Encoding` 和 `Vary` 标头。

![Fiddler 窗口，其中显示带有接受编码标头和值为 br 的请求的结果。 将 Vary 和内容编码标头添加到响应中。 响应已压缩。](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

将请求提交到带有 `Accept-Encoding: gzip` 标头的示例应用，并观察响应是否已压缩。 响应中存在 `Content-Encoding` 和 `Vary` 标头。

![显示带有接受编码标头和值 gzip 的请求结果的 Fiddler 窗口。 将 Vary 和内容编码标头添加到响应中。 响应已压缩。](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>提供程序

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Brotli 压缩提供程序

使用 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> 压缩[Brotli 压缩数据格式](https://tools.ietf.org/html/rfc7932)的响应。

如果未将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：

* 默认情况下，Brotli 压缩提供程序会随[Gzip 压缩提供程序](#gzip-compression-provider)一起添加到压缩提供程序的数组中。
* 当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。 如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

在显式添加任何压缩提供程序时，必须添加 Brotoli 压缩提供程序：

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

设置 <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>的压缩级别。 Brotli 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。 如果需要最有效的压缩，请将中间件配置为最佳压缩。

| Compression Level | 说明 |
| ----------------- | ----------- |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 压缩应该尽快完成，即使生成的输出未以最佳方式压缩。 |
| [CompressionLevel. NoCompression](xref:System.IO.Compression.CompressionLevel) | 不应执行压缩。 |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 即使压缩需要更长的时间，也应以最佳方式压缩响应。 |

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

### <a name="gzip-compression-provider"></a>Gzip 压缩提供程序

使用 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> 压缩具有[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)的响应。

如果未将压缩提供程序显式添加到 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>：

::: moniker range=">= aspnetcore-2.2"

* 默认情况下，Gzip 压缩提供程序随[Brotli 压缩提供程序](#brotli-compression-provider)一起添加到压缩提供程序的数组中。
* 当客户端支持 Brotli 压缩数据格式时，压缩默认为 Brotli 压缩。 如果客户端不支持 Brotli，则在客户端支持 Gzip 压缩时，压缩默认为 Gzip。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* 默认情况下，将 Gzip 压缩提供程序添加到压缩提供程序的数组。
* 当客户端支持 Gzip 压缩时，压缩默认为 Gzip。

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

在显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

设置 <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>的压缩级别。 Gzip 压缩提供程序默认为最快的压缩级别（[CompressionLevel](xref:System.IO.Compression.CompressionLevel)），这可能不会生成最有效的压缩。 如果需要最有效的压缩，请将中间件配置为最佳压缩。

| Compression Level | 说明 |
| ----------------- | ----------- |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 压缩应该尽快完成，即使生成的输出未以最佳方式压缩。 |
| [CompressionLevel. NoCompression](xref:System.IO.Compression.CompressionLevel) | 不应执行压缩。 |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 即使压缩需要更长的时间，也应以最佳方式压缩响应。 |

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

### <a name="custom-providers"></a>自定义提供程序

<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>创建自定义压缩实现。 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 表示此 `ICompressionProvider` 生成的内容编码。 中间件使用这些信息根据请求的 `Accept-Encoding` 标头中指定的列表来选择提供程序。

使用示例应用程序，客户端提交 `Accept-Encoding: mycustomcompression` 标头的请求。 中间件使用自定义压缩实现并返回 `Content-Encoding: mycustomcompression` 标头的响应。 客户端必须能够解压缩自定义编码，才能使自定义压缩实现正常运行。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

将请求提交到带有 `Accept-Encoding: mycustomcompression` 标头的示例应用，并观察响应标头。 响应中存在 `Vary` 和 `Content-Encoding` 标头。 示例不压缩响应正文（未显示）。 示例的 `CustomCompressionProvider` 类中没有压缩实现。 但是，该示例显示了实现此类压缩算法的位置。

![Fiddler 窗口，其中显示带有接受编码标头和值 mycustomcompression 的请求的结果。 将 Vary 和内容编码标头添加到响应中。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME 类型

中间件为压缩指定了一组默认的 MIME 类型：

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

用响应压缩中间件选项替换或追加 MIME 类型。 请注意，不支持通配符 MIME 类型，如 `text/*`。 示例应用为 `image/svg+xml` 添加了 MIME 类型，并对 ASP.NET Core 横幅图像（*横幅*）进行压缩和服务。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>安全协议压缩

可以使用默认情况下禁用 `EnableForHttps` 选项控制安全连接上的压缩响应。 在动态生成的页面中使用压缩可能会导致安全问题，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[漏洞](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。

## <a name="adding-the-vary-header"></a>添加 Vary 标头

根据 `Accept-Encoding` 标头压缩响应时，可能会有多个压缩版本的响应和未压缩的版本。 为了指示客户端和代理缓存有多个版本存在且应该存储，将使用 `Accept-Encoding` 的值添加 `Vary` 标头。 在 ASP.NET Core 2.0 或更高版本中，在对响应进行压缩时，中间件会自动添加 `Vary` 标头。

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx 反向代理后的中间件问题

当 Nginx 代理请求时，将删除 `Accept-Encoding` 标头。 删除 `Accept-Encoding` 标头会阻止中间件压缩响应。 有关详细信息，请参阅[NGINX：压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。 此问题通过[Nginx 的传递压缩（aspnet/BasicMiddleware #123）](https://github.com/aspnet/BasicMiddleware/issues/123)进行跟踪。

## <a name="working-with-iis-dynamic-compression"></a>使用 IIS 动态压缩

如果在要为应用禁用的服务器级别上配置了活动的 IIS 动态压缩模块，请使用*web.config*文件的附加功能禁用该模块。 有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。

## <a name="troubleshooting"></a>故障排除

使用[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或[Postman](https://www.getpostman.com/)之类的工具，通过该工具，可以设置 `Accept-Encoding` 请求标头并研究响应标头、大小和正文。 默认情况下，响应压缩中间件会压缩满足以下条件的响应：

::: moniker range=">= aspnetcore-2.2"

* `Accept-Encoding` 标头存在，其值为 `br`、`gzip`、`*`或自定义编码，此值与已建立的自定义压缩提供程序匹配。 该值不能为 `identity`，或者其质量值（qvalue，`q`）设置为0（零）。
* 必须设置 MIME 类型（`Content-Type`），并且该类型必须与 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>上配置的 MIME 类型匹配。
* 请求不能包含 `Content-Range` 标头。
* 除非在响应压缩中间件选项中配置了安全协议（https），否则请求必须使用不安全的协议（http）。 *启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* `Accept-Encoding` 标头存在，其值为 `gzip`、`*`或自定义编码，此值与已建立的自定义压缩提供程序匹配。 该值不能为 `identity`，或者其质量值（qvalue，`q`）设置为0（零）。
* 必须设置 MIME 类型（`Content-Type`），并且该类型必须与 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>上配置的 MIME 类型匹配。
* 请求不能包含 `Content-Range` 标头。
* 除非在响应压缩中间件选项中配置了安全协议（https），否则请求必须使用不安全的协议（http）。 *启用安全内容压缩时，请注意[上面所述](#compression-with-secure-protocol)的危险。*

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla 开发人员网络：接受编码](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 部分3.1.2.1： Content Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 部分4.2.3： Gzip 编码](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP 文件格式规范版本4。3](https://www.ietf.org/rfc/rfc1952.txt)
