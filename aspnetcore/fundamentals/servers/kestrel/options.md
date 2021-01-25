---
title: 为 ASP.NET Core Kestrel Web 服务器配置选项
author: rick-anderson
description: 了解如何为 Kestrel（跨平台 ASP.NET Core Web 服务器）配置选项。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- appsettings.json
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 198d509a68224077d3764cc836121b89e96c6853
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253830"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a>为 ASP.NET Core Kestrel Web 服务器配置选项

Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。

若要在调用 `ConfigureWebHostDefaults` 后提供其他配置，请使用 `ConfigureKestrel`：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。 `Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。

下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

在本文后面的示例中，Kestrel 选项是采用 C# 代码配置的。 还可以使用 [配置提供程序](xref:fundamentals/configuration/index)设置 Kestrel 选项。 例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置：

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 和 [终结点配置](xref:fundamentals/servers/kestrel/endpoints) 可以通过配置提供程序进行配置。 其余的 Kestrel 配置必须采用 C# 代码进行配置。

使用以下方法之一：

* 在 `Startup.ConfigureServices` 中配置 Kestrel：

  1. 将 `IConfiguration` 的实例注入到 `Startup` 类中。 下面的示例假定注入的配置已分配给 `Configuration` 属性。
  2. 在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* 构建主机时配置 Kestrel：

  在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。

## <a name="general-limits"></a>一般限制

### <a name="keep-alive-timeout"></a>保持活动状态超时

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。 默认值为 2 分钟。

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>客户端最大连接数

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。 连接升级后，不会计入 `MaxConcurrentConnections` 限制。

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

默认情况下，最大连接数不受限制 (NULL)。

### <a name="maximum-request-body-size"></a>请求正文最大大小

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。

在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

以下示例演示如何为每个请求上的应用配置约束：

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

在中间件中替代特定请求的设置：

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

如果应用在开始读取请求后配置请求限制，则会引发异常。 `IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。

当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，Kestrel 的请求正文大小限制处于禁用状态。 IIS 已设置限制。

### <a name="minimum-request-body-data-rate"></a>请求正文最小数据速率

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。 如果速率低于最小值，则连接超时。宽限期是 Kestrel 允许客户端将其发送速率提升到最小值的时间量。 在此期间不会检查速率。 宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。

默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。

最小速率也适用于响应。 除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。

以下示例演示如何在 Program.cs 中配置最小数据速率：

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

在中间件中替代每个请求的最低速率限制：

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

用于 HTTP/2 请求的 `HttpContext.Features` 中不存在先前示例中引用的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>。 由于协议支持请求多路复用，因此 HTTP/2 通常不支持基于每个请求修改速率限制。 不过，用于 HTTP/2 请求的 `HttpContext.Features` 中仍存在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>，因为仍可以通过将 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 设置为 `null`（甚至对于 HTTP/2 请求），按请求完全禁用读取速率限制。 对于给定 HTTP/2 请求，尝试读取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或尝试将它设置为除 `null` 以外的值会导致 `NotSupportedException` 抛出。

通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。

### <a name="request-headers-timeout"></a>请求标头超时

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

获取或设置服务器接收请求标头所花费的最大时间量。 默认值为 30 秒。

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a>HTTP/2 限制

### <a name="maximum-streams-per-connection"></a>每个连接的最大流

`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。 拒绝过多的流。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

默认值为 100。

### <a name="header-table-size"></a>标题表大小

HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。 `Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。 该值以八位字节提供，且必须大于零 (0)。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

默认值为 4096。

### <a name="maximum-frame-size"></a>最大帧大小

`Http2.MaxFrameSize` 表示服务器接收或发送的 HTTP/2 连接帧有效负载的最大允许大小。 该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

默认值为 2^14 (16,384)。

### <a name="maximum-request-header-size"></a>最大请求标头大小

`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。 此限制适用于名称和值的压缩和未压缩表示形式。 该值必须大于零 (0)。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

默认值为 8,192。

### <a name="initial-connection-window-size"></a>初始连接窗口大小

`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。 请求也受 `Http2.InitialStreamWindowSize` 限制。 该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

默认值为 128 KB (131,072)。

### <a name="initial-stream-window-size"></a>初始流窗口大小

`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。 请求也受 `Http2.InitialConnectionWindowSize` 限制。 该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

默认值为 96 KB (98,304)。

### <a name="http2-keep-alive-ping-configuration"></a>HTTP/2 保持活动 ping 配置

Kestrel 可以配置为向连接的客户端发送 HTTP/2 ping。 HTTP/2 ping 有多种用途：

* 使空闲连接保持活动状态。 某些客户端和代理服务器会关闭空闲的连接。 HTTP/2 ping 是对连接执行的活动，可防止空闲连接被关闭。
* 关闭不正常的连接。 服务器会关闭在配置的时间内客户端未响应保持活动 ping 的连接。

与 HTTP/2 保持活动 ping 关联的配置选项有两个：

* `Http2.KeepAlivePingInterval` 是配置 ping 间隔的 `TimeSpan`。 如果服务器在此时间段内没有收到任何帧，则服务器会向客户端发送保持活动 ping。 将此选项设置为 `TimeSpan.MaxValue` 时，会禁用保持活动 ping。 默认值为 `TimeSpan.MaxValue`。
* `Http2.KeepAlivePingTimeout` 是配置 ping 超时的 `TimeSpan`。 如果服务器在此超时期间没有收到任何帧（如响应 ping），则连接将关闭。 将此选项设置为 `TimeSpan.MaxValue` 时，会禁用保持活动状态超时。 默认值为 20 秒。

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a>其他选项

### <a name="synchronous-io"></a>同步 I/O

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 I/O。 默认值为 `false`。

> [!WARNING]
> 大量阻止同步 I/O 的操作可能会导致线程池资源不足，进而导致应用无响应。 仅在使用不支持异步 I/O 的库时，才启用 `AllowSynchronousIO`。

以下示例会启用同步 I/O：

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

有关其他 Kestrel 选项和限制的信息，请参阅：

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
