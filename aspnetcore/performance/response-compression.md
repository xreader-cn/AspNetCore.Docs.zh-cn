---
title: 在 ASP.NET Core 中的响应压缩
author: guardrex
description: 了解如何响应压缩以及如何在 ASP.NET Core 应用中使用响应压缩中间件。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: performance/response-compression
ms.openlocfilehash: a9f72a6816298b11e7b7d30b2b4bd44083baab3a
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099034"
---
# <a name="response-compression-in-aspnet-core"></a>在 ASP.NET Core 中的响应压缩

作者：[Luke Latham](https://github.com/guardrex)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples)（[如何下载](xref:index#how-to-download-a-sample)）

网络带宽是一种有限的资源。 通常减少响应的大小通常显著增加的应用的响应能力。 若要减少有效负载大小的一种方法是压缩应用程序的响应。

## <a name="when-to-use-response-compression-middleware"></a>何时使用响应压缩中间件

使用 IIS、 Apache 或 Nginx 中的基于服务器的响应压缩技术。 中间件的性能可能不匹配，服务器模块。 [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)服务器和[Kestrel](xref:fundamentals/servers/kestrel) server 当前不提供内置的压缩支持。

如果你已，使用响应压缩中间件：

* 无法使用以下基于服务器的压缩技术：
  * [IIS 动态压缩模块](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate 模块](http://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx 压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 直接在上托管：
  * [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）
  * [Kestrel 服务器](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>响应压缩

通常情况下，可以从响应压缩受益本身不压缩任何响应。 响应本身不压缩通常包括：CSS、 JavaScript、 HTML、 XML 和 JSON。 不应压缩本机压缩的资产，如 PNG 文件。 如果您尝试进一步将压缩本机压缩的响应，小型进一步降低大小和传输时间将有可能屏蔽处理压缩花费的时间。 不压缩小于大约 150 1000年字节 （具体取决于文件的内容和压缩的效率） 的文件。 压缩小文件的开销可能会产生大于未压缩的文件的压缩的文件。

客户端时客户端可以处理压缩的内容，必须通过发送通知的功能的服务器`Accept-Encoding`与请求的标头。 当一台服务器发送压缩的内容时，它必须包括中的信息`Content-Encoding`标头压缩的响应的编码方式。 下表中显示内容由中间件支持指定的编码内容。

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding` 标头值 | 支持的中间件 | 描述 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 是 （默认值）        | [Brotli 压缩的数据格式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | 否                   | [DEFLATE 压缩的数据格式](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | 否                   | [W3C 有效 XML 交换](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 是                  | [Gzip 文件格式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | 是                  | "没有 encoding"的标识符：响应必须不进行编码。 |
| `pack200-gzip`                  | 否                   | [Java 存档文件的网络传输格式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | 是                  | 编码不显式请求任何可用内容 |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding` 标头值 | 支持的中间件 | 描述 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 否                   | [Brotli 压缩的数据格式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | 否                   | [DEFLATE 压缩的数据格式](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | 否                   | [W3C 有效 XML 交换](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 是 （默认值）        | [Gzip 文件格式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | 是                  | "没有 encoding"的标识符：响应必须不进行编码。 |
| `pack200-gzip`                  | 否                   | [Java 存档文件的网络传输格式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | 是                  | 编码不显式请求任何可用内容 |

::: moniker-end

有关详细信息，请参阅[IANA 官方内容编码列表](http://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)。

中间件可以添加额外的压缩的自定义的提供程序`Accept-Encoding`标头值。 有关详细信息，请参阅[自定义提供程序](#custom-providers)下面。

中间件是能够对质量值作出反应 (qvalue， `q`) 权重时由客户端发送，以确定压缩方案的优先级。 有关详细信息，请参阅[RFC 7231:接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)。

压缩算法会有所压缩速度和压缩的效率之间的权衡。 *有效性*在此上下文中引用的输出大小在压缩之后的。 最小大小通过最*最佳*压缩。

在请求中所涉及的标头，发送、 缓存和接收压缩的内容是下表中所述。

| Header             | 角色 |
| ------------------ | ---- |
| `Accept-Encoding`  | 从客户端发送到服务器以指示编码方案可向客户端接受的内容。 |
| `Content-Encoding` | 从服务器发送到客户端以指示负载中的内容的编码。 |
| `Content-Length`   | 压缩时，`Content-Length`删除标头，因为压缩响应的正文内容更改。 |
| `Content-MD5`      | 压缩时，`Content-MD5`删除标头，因为正文内容已更改，哈希将不再有效。 |
| `Content-Type`     | 指定内容的 MIME 的类型。 每个响应应指定其`Content-Type`。 中间件将检查此值以确定是否应压缩响应。 中间件指定一组[默认 MIME 类型](#mime-types)，可以对进行编码，但您可以替换或添加 MIME 类型。 |
| `Vary`             | 时，值为服务器发送`Accept-Encoding`到客户端和代理服务器，`Vary`标头指示与客户端或应缓存的代理 （有所不同） 响应值的基础`Accept-Encoding`的请求标头。 返回的内容的结果`Vary: Accept-Encoding`标头是同时压缩和未压缩的响应将单独进行缓存。 |

探索的功能与响应压缩中间件[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples)。 此示例演示了：

* 使用 Gzip 和自定义压缩提供程序的应用程序响应的压缩。
* 如何将 MIME 类型添加到压缩的 MIME 类型的默认列表。

## <a name="package"></a>package

::: moniker range=">= aspnetcore-2.1"

若要在项目中包含中间件，添加对的引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，其中包括[Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

若要在项目中包含中间件，添加对的引用[Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)，其中包括[Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

若要在项目中包含中间件，添加对的引用[Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)包。

::: moniker-end

## <a name="configuration"></a>配置

::: moniker range=">= aspnetcore-2.2"

下面的代码演示如何启用响应压缩中间件默认 MIME 类型和压缩提供程序 ([Brotli](#brotli-compression-provider)并[Gzip](#gzip-compression-provider)):

::: moniker-end

::: moniker range="< aspnetcore-2.2"

下面的代码演示如何启用默认 MIME 类型响应压缩中间件和[Gzip 压缩提供程序](#gzip-compression-provider):

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

注意： 

* `app.UseResponseCompression` 必须在`app.UseMvc`之前调用。
* 使用一种工具，如[Fiddler](http://www.telerik.com/fiddler)， [Firebug](http://getfirebug.com/)，或[Postman](https://www.getpostman.com/)设置`Accept-Encoding`请求标头并研究响应标头、 大小和正文。

向示例应用程序而无需提交一个申请`Accept-Encoding`标头，并观察的响应是未压缩。 `Content-Encoding`和`Vary`标头不存在的响应。

![Fiddler 窗口，其中显示不含 Accept-encoding 标头请求的结果。 不会压缩响应。](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

向具有示例应用程序提交一个申请`Accept-Encoding: br`标头 （Brotli 压缩），并观察压缩响应。 `Content-Encoding`和`Vary`均存在于响应标头。

![Fiddler 窗口，其中显示 Accept-encoding 标头与请求的结果和 b 的值。 变化和内容编码标头添加到响应中。 压缩响应。](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

向具有示例应用程序提交一个申请`Accept-Encoding: gzip`标头，并观察压缩响应。 `Content-Encoding`和`Vary`均存在于响应标头。

![Fiddler 窗口，其中显示 Accept-encoding 标头与请求的结果，值为 gzip。 变化和内容编码标头添加到响应中。 压缩响应。](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>提供程序

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Brotli 压缩提供程序

使用`BrotliCompressionProvider`若要压缩的响应[Brotli 压缩的数据格式](https://tools.ietf.org/html/rfc7932)。

如果不压缩到提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* Brotli 压缩提供程序默认情况下添加到与压缩提供程序的数组[Gzip 压缩提供程序](#gzip-compression-provider)。
* 当客户端支持 Brotli 压缩的数据格式时，则将默认压缩为 Brotli 压缩。 如果客户端不支持 Brotli 时客户端支持 Gzip 压缩, 压缩默认设置为 Gzip。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

显式添加任何压缩提供程序时，必须添加 Brotoli 压缩提供程序：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

设置压缩级别与`BrotliCompressionProviderOptions`。 Brotli 压缩提供程序默认为最快的压缩级别 ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel))，这可能不会产生最高效的压缩。 如果需要最高效的压缩，则配置理想的压缩的中间件。

| 压缩级别 | 描述 |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | 即使不以最佳方式压缩生成的输出，应尽可能快地完成压缩。 |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | 应该在不进行压缩。 |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | 响应应以最佳方式压缩，即使压缩操作将耗费更多时间来完成。 |

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

使用<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>若要压缩的响应[Gzip 文件格式](https://tools.ietf.org/html/rfc1952)。

如果不压缩到提供程序显式添加到<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

::: moniker range=">= aspnetcore-2.2"

* Gzip 压缩提供程序默认情况下添加到与压缩提供程序的数组[Brotli 压缩提供程序](#brotli-compression-provider)。
* 当客户端支持 Brotli 压缩的数据格式时，则将默认压缩为 Brotli 压缩。 如果客户端不支持 Brotli 时客户端支持 Gzip 压缩, 压缩默认设置为 Gzip。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* 默认情况下，Gzip 压缩提供程序添加到压缩提供程序的数组。
* 压缩默认值为 Gzip 时客户端支持 Gzip 压缩。

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

显式添加任何压缩提供程序时，必须添加 Gzip 压缩提供程序：

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

设置压缩级别与<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>。 Gzip 压缩提供程序默认为最快的压缩级别 ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel))，这可能不会产生最高效的压缩。 如果需要最高效的压缩，则配置理想的压缩的中间件。

| 压缩级别 | 描述 |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | 即使不以最佳方式压缩生成的输出，应尽可能快地完成压缩。 |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | 应该在不进行压缩。 |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | 响应应以最佳方式压缩，即使压缩操作将耗费更多时间来完成。 |

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

创建自定义压缩实现<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>。 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>表示的内容编码此`ICompressionProvider`生成。 中间件使用此信息来选择基于列表中指定的提供程序`Accept-Encoding`的请求标头。

使用示例应用程序，客户端提交的请求`Accept-Encoding: mycustomcompression`标头。 中间件使用自定义压缩的实现，并返回与响应`Content-Encoding: mycustomcompression`标头。 客户端必须能够将解压缩的自定义压缩实现以处理顺序中的自定义编码。

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

```csharp
public class CustomCompressionProvider : ICompressionProvider
{
    public string EncodingName => "mycustomcompression";
    public bool SupportsFlush => true;

    public Stream CreateStream(Stream outputStream)
    {
        // Create a custom compression stream wrapper here
        return outputStream;
    }
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6,12-15)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6,12-15)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

向具有示例应用程序提交一个申请`Accept-Encoding: mycustomcompression`标头，并观察响应标头。 `Vary`和`Content-Encoding`均存在于响应标头。 此示例将不会压缩响应正文 （未显示）。 不压缩的实现中存在`CustomCompressionProvider`类的示例。 但是，此示例演示您可以用来实现此类的压缩算法。

![显示的 Accept-encoding 标头与请求的结果，值为 mycustomcompression fiddler 窗口。 变化和内容编码标头添加到响应中。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME 类型

中间件指定一组默认的压缩的 MIME 类型：

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

替换还是追加使用响应压缩中间件的选项的 MIME 类型。 请注意该通配符 MIME 类型，如`text/*`不受支持。 示例应用程序添加的 MIME 类型`image/svg+xml`和压缩，并提供横幅图像的 ASP.NET Core (*banner.svg*)。

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>使用安全协议的压缩

可以使用控制通过安全连接的压缩的响应`EnableForHttps`选项，它默认处于禁用状态。 使用动态生成的页面压缩可能会导致安全问题如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))并[违反](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。

## <a name="adding-the-vary-header"></a>添加将 Vary 标头

::: moniker range=">= aspnetcore-2.0"

当压缩响应基于`Accept-Encoding`标头，有可能多个压缩的版本的响应的和未压缩的版本。 若要指示客户端和代理服务器缓存，多个版本存在，并且应存储`Vary`标头添加与`Accept-Encoding`值。 在 ASP.NET Core 2.0 或更高版本，该中间件将添加`Vary`压缩响应时自动标头。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

当压缩响应基于`Accept-Encoding`标头，有可能多个压缩的版本的响应的和未压缩的版本。 若要指示客户端和代理服务器缓存，多个版本存在，并且应存储`Vary`标头添加与`Accept-Encoding`值。 在 ASP.NET Core 1.x 添加`Vary`手动完成到响应的标头：

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>当位于 Nginx 反向代理中间件问题

如果请求的是代理的 Nginx`Accept-Encoding`删除标头。 删除`Accept-Encoding`标头可防止中间件压缩响应。 有关详细信息，请参阅[NGINX:压缩和解压缩](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)。 此问题由跟踪[找出传递压缩 Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123)。

## <a name="working-with-iis-dynamic-compression"></a>使用 IIS 动态压缩

如果您有一个活动 IIS 动态压缩模块你想要禁用的应用程序的服务器级别配置，禁用对模块进行补充*web.config*文件。 有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。

## <a name="troubleshooting"></a>疑难解答

使用之类的工具[Fiddler](https://www.telerik.com/fiddler)， [Firebug](https://getfirebug.com/)，或[Postman](https://www.getpostman.com/)，这样便可以设置`Accept-Encoding`请求标头并研究响应标头、 大小和正文。 默认情况下，响应压缩中间件将压缩满足以下条件的响应：

::: moniker range=">= aspnetcore-2.2"

* `Accept-Encoding`值为标头，则`br`， `gzip`， `*`，或与已建立的自定义压缩提供程序相匹配的自定义编码。 值不能`identity`或具有一个质量值 (qvalue， `q`) 设置为 0 （零）。
* MIME 类型 (`Content-Type`) 必须设置并且必须匹配在配置的 MIME 类型<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>。
* 请求必须包括`Content-Range`标头。
* 请求必须使用不安全的协议 (http)，除非在响应压缩中间件选项中配置安全协议 (https)。 *请注意危险[上面所述](#compression-with-secure-protocol)启用安全内容压缩时。*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* `Accept-Encoding`值为标头，则`gzip`， `*`，或与已建立的自定义压缩提供程序相匹配的自定义编码。 值不能`identity`或具有一个质量值 (qvalue， `q`) 设置为 0 （零）。
* MIME 类型 (`Content-Type`) 必须设置并且必须匹配在配置的 MIME 类型<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>。
* 请求必须包括`Content-Range`标头。
* 请求必须使用不安全的协议 (http)，除非在响应压缩中间件选项中配置安全协议 (https)。 *请注意危险[上面所述](#compression-with-secure-protocol)启用安全内容压缩时。*

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla 开发人员网络：接受编码](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 节 3.1.2.1:内容 Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 4.2.3 节：Gzip 编码](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP 文件格式规范版本 4.3](http://www.ietf.org/rfc/rfc1952.txt)
