---
title: ASP.NET Core 中的 Kestrel Web 服务器实现
author: guardrex
description: 了解跨平台 ASP.NET Core Web 服务器 Kestrel。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 9fbf0ec93634100fccef279fc7cad92cb1420e84
ms.sourcegitcommit: 991442dfb16ef08a0aae05bc79f9e9a2d819c587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75492598"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="c5e8e-103">ASP.NET Core 中的 Kestrel Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="c5e8e-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="c5e8e-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)[Stephen Halter](https://twitter.com/halter73) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c5e8e-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5e8e-105">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c5e8e-106">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c5e8e-107">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c5e8e-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c5e8e-108">HTTPS</span></span>
* <span data-ttu-id="c5e8e-109">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="c5e8e-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c5e8e-110">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="c5e8e-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="c5e8e-111">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="c5e8e-112">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="c5e8e-113">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c5e8e-114">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="c5e8e-115">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="c5e8e-115">HTTP/2 support</span></span>

<span data-ttu-id="c5e8e-116">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c5e8e-117">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="c5e8e-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="c5e8e-118">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="c5e8e-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="c5e8e-119">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="c5e8e-120">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c5e8e-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="c5e8e-121">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="c5e8e-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c5e8e-122">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="c5e8e-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c5e8e-123">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="c5e8e-124">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c5e8e-125">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c5e8e-126">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="c5e8e-127">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c5e8e-128">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="c5e8e-129">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c5e8e-130">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="c5e8e-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c5e8e-131">可以单独使用 Kestrel，也可以将其与反向代理服务器  （如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c5e8e-132">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c5e8e-133">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c5e8e-135">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c5e8e-137">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c5e8e-138">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c5e8e-139">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c5e8e-140">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c5e8e-141">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c5e8e-142">反向代理：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-142">A reverse proxy:</span></span>

* <span data-ttu-id="c5e8e-143">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c5e8e-144">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c5e8e-145">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c5e8e-146">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c5e8e-147">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c5e8e-148">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c5e8e-149">ASP.NET Core 应用中的 Kestrel</span><span class="sxs-lookup"><span data-stu-id="c5e8e-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c5e8e-150">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c5e8e-151">在“Program.cs”中，  <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="c5e8e-152">有关生成主机的详细信息，请参阅 <xref:fundamentals/host/generic-host#set-up-a-host> 的“设置主机”和“默认生成器设置”部分   。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="c5e8e-153">若要在调用 `ConfigureWebHostDefaults` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="c5e8e-154">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="c5e8e-154">Kestrel options</span></span>

<span data-ttu-id="c5e8e-155">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c5e8e-156">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c5e8e-157">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c5e8e-158">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c5e8e-159">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-159">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c5e8e-160">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置   ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-160">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="c5e8e-161">使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-161">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c5e8e-162">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-162">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c5e8e-163">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-163">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c5e8e-164">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-164">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c5e8e-165">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-165">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="c5e8e-166">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-166">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c5e8e-167">在 Program.cs  中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-167">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="c5e8e-168">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-168">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c5e8e-169">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="c5e8e-169">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c5e8e-170">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-170">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c5e8e-171">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-171">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="c5e8e-172">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="c5e8e-172">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c5e8e-173">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-173">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="c5e8e-174">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-174">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c5e8e-175">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-175">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="c5e8e-176">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-176">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c5e8e-177">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-177">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c5e8e-178">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-178">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c5e8e-179">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-179">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c5e8e-180">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-180">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="c5e8e-181">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-181">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c5e8e-182">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-182">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c5e8e-183">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-183">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c5e8e-184">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-184">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c5e8e-185">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="c5e8e-185">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c5e8e-186">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-186">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c5e8e-187">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-187">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c5e8e-188">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-188">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c5e8e-189">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-189">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c5e8e-190">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-190">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c5e8e-191">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-191">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c5e8e-192">以下示例演示如何在 Program.cs 中配置最小数据速率  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-192">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="c5e8e-193">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-193">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="c5e8e-194">用于 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>，因为鉴于协议支持请求多路复用，HTTP/2 通常不支持按请求修改速率限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-194">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="c5e8e-195">不过，用于 HTTP/2 请求的 `HttpContext.Features` 中仍存在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>，因为仍可以通过将 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 设置为 `null`（甚至对于 HTTP/2 请求），按请求完全禁用  读取速率限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-195">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="c5e8e-196">对于给定 HTTP/2 请求，尝试读取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或尝试将它设置为除 `null` 以外的值会导致 `NotSupportedException` 抛出。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-196">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="c5e8e-197">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-197">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="c5e8e-198">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="c5e8e-198">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c5e8e-199">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-199">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c5e8e-200">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-200">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="c5e8e-201">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="c5e8e-201">Maximum streams per connection</span></span>

<span data-ttu-id="c5e8e-202">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-202">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="c5e8e-203">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-203">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="c5e8e-204">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-204">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="c5e8e-205">标题表大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-205">Header table size</span></span>

<span data-ttu-id="c5e8e-206">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-206">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="c5e8e-207">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-207">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="c5e8e-208">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-208">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="c5e8e-209">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-209">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="c5e8e-210">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-210">Maximum frame size</span></span>

<span data-ttu-id="c5e8e-211">`Http2.MaxFrameSize` 表示服务器接收或发送的 HTTP/2 连接帧有效负载的最大允许大小。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-211">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="c5e8e-212">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-212">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="c5e8e-213">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-213">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="c5e8e-214">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-214">Maximum request header size</span></span>

<span data-ttu-id="c5e8e-215">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-215">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="c5e8e-216">此限制适用于名称和值的压缩和未压缩表示形式。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-216">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="c5e8e-217">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-217">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="c5e8e-218">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-218">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="c5e8e-219">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-219">Initial connection window size</span></span>

<span data-ttu-id="c5e8e-220">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-220">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="c5e8e-221">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-221">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c5e8e-222">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-222">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="c5e8e-223">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-223">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="c5e8e-224">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-224">Initial stream window size</span></span>

<span data-ttu-id="c5e8e-225">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-225">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="c5e8e-226">请求也受 `Http2.InitialConnectionWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-226">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="c5e8e-227">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-227">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="c5e8e-228">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-228">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="c5e8e-229">同步 IO</span><span class="sxs-lookup"><span data-stu-id="c5e8e-229">Synchronous IO</span></span>

<span data-ttu-id="c5e8e-230"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 IO。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-230"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="c5e8e-231">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-231">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="c5e8e-232">大量的阻止同步 IO 操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-232">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c5e8e-233">仅在使用不支持异步 IO 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-233">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="c5e8e-234">下面的示例启用同步 IO：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-234">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="c5e8e-235">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-235">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c5e8e-236">终结点配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-236">Endpoint configuration</span></span>

<span data-ttu-id="c5e8e-237">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-237">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c5e8e-238">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-238">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c5e8e-239">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-239">Specify URLs using the:</span></span>

* <span data-ttu-id="c5e8e-240">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-240">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c5e8e-241">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-241">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c5e8e-242">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-242">`urls` host configuration key.</span></span>
* <span data-ttu-id="c5e8e-243">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-243">`UseUrls` extension method.</span></span>

<span data-ttu-id="c5e8e-244">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-244">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c5e8e-245">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000; http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-245">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c5e8e-246">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-246">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c5e8e-247">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-247">A development certificate is created:</span></span>

* <span data-ttu-id="c5e8e-248">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-248">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c5e8e-249">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-249">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c5e8e-250">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-250">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c5e8e-251">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-251">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c5e8e-252">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-252">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c5e8e-253">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-253">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c5e8e-254">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-254">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c5e8e-255">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="c5e8e-255">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c5e8e-256">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-256">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c5e8e-257">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-257">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="c5e8e-258">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。 </span><span class="sxs-lookup"><span data-stu-id="c5e8e-258">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c5e8e-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="c5e8e-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c5e8e-260">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-260">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c5e8e-261">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-261">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="c5e8e-262">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。 </span><span class="sxs-lookup"><span data-stu-id="c5e8e-262">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="c5e8e-263">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c5e8e-263">Configure(IConfiguration)</span></span>

<span data-ttu-id="c5e8e-264">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-264">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c5e8e-265">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-265">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c5e8e-266">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c5e8e-266">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c5e8e-267">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-267">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c5e8e-268">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-268">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c5e8e-269">`UseHttps` &ndash; 将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-269">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c5e8e-270">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-270">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="c5e8e-271">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-271">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c5e8e-272">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-272">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c5e8e-273">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-273">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c5e8e-274">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-274">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c5e8e-275">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-275">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c5e8e-276">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-276">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c5e8e-277">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-277">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c5e8e-278">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-278">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c5e8e-279">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-279">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c5e8e-280">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-280">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c5e8e-281">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-281">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c5e8e-282">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-282">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c5e8e-283">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-283">Supported configurations described next:</span></span>

* <span data-ttu-id="c5e8e-284">无配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-284">No configuration</span></span>
* <span data-ttu-id="c5e8e-285">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="c5e8e-285">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c5e8e-286">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="c5e8e-286">Change the defaults in code</span></span>

<span data-ttu-id="c5e8e-287">*无配置*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-287">*No configuration*</span></span>

<span data-ttu-id="c5e8e-288">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-288">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c5e8e-289">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-289">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c5e8e-290">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-290">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c5e8e-291">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-291">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c5e8e-292">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-292">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c5e8e-293">在以下 appsettings.json 示例中  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-293">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="c5e8e-294">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）  。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-294">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c5e8e-295">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书    。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-295">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="c5e8e-296">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书   。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-296">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c5e8e-297">例如，可将 Certificates > Default 证书指定为   ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-297">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c5e8e-298">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-298">Schema notes:</span></span>

* <span data-ttu-id="c5e8e-299">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-299">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c5e8e-300">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-300">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c5e8e-301">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-301">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c5e8e-302">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-302">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c5e8e-303">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-303">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c5e8e-304">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-304">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c5e8e-305">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-305">The `Certificate` section is optional.</span></span> <span data-ttu-id="c5e8e-306">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-306">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c5e8e-307">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-307">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c5e8e-308">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书     。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-308">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c5e8e-309">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-309">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c5e8e-310">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-310">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="c5e8e-311">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-311">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c5e8e-312">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-312">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c5e8e-313">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-313">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c5e8e-314">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-314">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c5e8e-315">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-315">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c5e8e-316">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-316">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c5e8e-317">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-317">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c5e8e-318">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-318">*Change the defaults in code*</span></span>

<span data-ttu-id="c5e8e-319">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-319">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c5e8e-320">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-320">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="c5e8e-321">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-321">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c5e8e-322">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-322">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c5e8e-323">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-323">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c5e8e-324">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-324">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c5e8e-325">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-325">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c5e8e-326">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-326">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c5e8e-327">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-327">SNI support requires:</span></span>

* <span data-ttu-id="c5e8e-328">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-328">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c5e8e-329">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-329">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c5e8e-330">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-330">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c5e8e-331">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-331">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c5e8e-332">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-332">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="c5e8e-333">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="c5e8e-333">Connection logging</span></span>

<span data-ttu-id="c5e8e-334">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-334">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c5e8e-335">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-335">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c5e8e-336">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-336">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c5e8e-337">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-337">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c5e8e-338">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="c5e8e-338">Bind to a TCP socket</span></span>

<span data-ttu-id="c5e8e-339"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-339">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="c5e8e-340">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-340">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c5e8e-341">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-341">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c5e8e-342">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="c5e8e-342">Bind to a Unix socket</span></span>

<span data-ttu-id="c5e8e-343">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-343">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="c5e8e-344">端口 0</span><span class="sxs-lookup"><span data-stu-id="c5e8e-344">Port 0</span></span>

<span data-ttu-id="c5e8e-345">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-345">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c5e8e-346">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-346">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c5e8e-347">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-347">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c5e8e-348">限制</span><span class="sxs-lookup"><span data-stu-id="c5e8e-348">Limitations</span></span>

<span data-ttu-id="c5e8e-349">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-349">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c5e8e-350">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="c5e8e-350">`--urls` command-line argument</span></span>
* <span data-ttu-id="c5e8e-351">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="c5e8e-351">`urls` host configuration key</span></span>
* <span data-ttu-id="c5e8e-352">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="c5e8e-352">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c5e8e-353">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-353">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c5e8e-354">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-354">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c5e8e-355">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-355">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c5e8e-356">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-356">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c5e8e-357">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-357">IIS endpoint configuration</span></span>

<span data-ttu-id="c5e8e-358">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-358">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c5e8e-359">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-359">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="c5e8e-360">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="c5e8e-360">ListenOptions.Protocols</span></span>

<span data-ttu-id="c5e8e-361">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-361">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="c5e8e-362">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-362">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="c5e8e-363">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="c5e8e-363">`HttpProtocols` enum value</span></span> | <span data-ttu-id="c5e8e-364">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="c5e8e-364">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="c5e8e-365">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-365">HTTP/1.1 only.</span></span> <span data-ttu-id="c5e8e-366">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-366">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="c5e8e-367">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-367">HTTP/2 only.</span></span> <span data-ttu-id="c5e8e-368">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-368">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="c5e8e-369">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-369">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="c5e8e-370">HTTP/2 要求客户端在 TLS [应用层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手过程中选择 HTTP/2；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-370">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="c5e8e-371">任何终结点的默认 `ListenOptions.Protocols` 值都为 `HttpProtocols.Http1AndHttp2`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-371">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="c5e8e-372">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-372">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="c5e8e-373">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c5e8e-373">TLS version 1.2 or later</span></span>
* <span data-ttu-id="c5e8e-374">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="c5e8e-374">Renegotiation disabled</span></span>
* <span data-ttu-id="c5e8e-375">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="c5e8e-375">Compression disabled</span></span>
* <span data-ttu-id="c5e8e-376">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-376">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="c5e8e-377">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 最小 224 位</span><span class="sxs-lookup"><span data-stu-id="c5e8e-377">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="c5e8e-378">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="c5e8e-378">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="c5e8e-379">密码套件未列入阻止列表</span><span class="sxs-lookup"><span data-stu-id="c5e8e-379">Cipher suite not blacklisted</span></span>

<span data-ttu-id="c5e8e-380">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-380">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="c5e8e-381">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-381">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="c5e8e-382">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-382">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="c5e8e-383">使用连接中间件，针对特定密码的每个连接筛选 TLS 握手（如有必要）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-383">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="c5e8e-384">下面的示例针对应用不支持的任何密码算法引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-384">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="c5e8e-385">或者，定义 [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 并将其与可接受的密码套件列表进行比较。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-385">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="c5e8e-386">没有哪种加密是使用 [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 密码算法。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-386">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="c5e8e-387">连接筛选也可以通过 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda 进行配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-387">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="c5e8e-388">在 Linux 上，<xref:System.Net.Security.CipherSuitesPolicy> 可用于针对每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-388">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="c5e8e-389">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-389">*Set the protocol from configuration*</span></span>

<span data-ttu-id="c5e8e-390">`CreateDefaultBuilder` 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-390">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="c5e8e-391">以下 appsettings.json 示例将 HTTP/1.1 建立为所有终结点的默认连接协议  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-391">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="c5e8e-392">以下 appsettings.json 示例将 HTTP/1.1 建立为所有指定终结点的连接协议  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-392">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="c5e8e-393">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-393">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="c5e8e-394">传输配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-394">Transport configuration</span></span>

<span data-ttu-id="c5e8e-395">对于需要使用 Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) 的项目：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-395">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="c5e8e-396">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-396">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="c5e8e-397">调用 `IWebHostBuilder` 上的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-397">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="c5e8e-398">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="c5e8e-398">URL prefixes</span></span>

<span data-ttu-id="c5e8e-399">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-399">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c5e8e-400">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-400">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c5e8e-401">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-401">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c5e8e-402">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="c5e8e-402">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c5e8e-403">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-403">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c5e8e-404">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="c5e8e-404">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c5e8e-405">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-405">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c5e8e-406">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="c5e8e-406">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c5e8e-407">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-407">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c5e8e-408">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-408">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c5e8e-409">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-409">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c5e8e-410">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-410">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c5e8e-411">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="c5e8e-411">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c5e8e-412">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-412">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c5e8e-413">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-413">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c5e8e-414">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-414">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c5e8e-415">主机筛选</span><span class="sxs-lookup"><span data-stu-id="c5e8e-415">Host filtering</span></span>

<span data-ttu-id="c5e8e-416">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-416">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c5e8e-417">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-417">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c5e8e-418">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-418">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c5e8e-419">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-419">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c5e8e-420">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-420">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c5e8e-421">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包为 ASP.NET Core 应用隐式提供。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-421">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="c5e8e-422">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-422">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c5e8e-423">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-423">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c5e8e-424">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键   。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-424">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="c5e8e-425">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-425">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c5e8e-426">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-426">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c5e8e-427">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-427">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c5e8e-428">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-428">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c5e8e-429">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-429">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c5e8e-430">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-430">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c5e8e-431">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-431">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c5e8e-432">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-432">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c5e8e-433">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-433">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c5e8e-434">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-434">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c5e8e-435">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c5e8e-435">HTTPS</span></span>
* <span data-ttu-id="c5e8e-436">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="c5e8e-436">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c5e8e-437">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="c5e8e-437">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="c5e8e-438">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-438">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="c5e8e-439">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-439">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="c5e8e-440">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-440">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c5e8e-441">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-441">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="c5e8e-442">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="c5e8e-442">HTTP/2 support</span></span>

<span data-ttu-id="c5e8e-443">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-443">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c5e8e-444">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="c5e8e-444">Operating system&dagger;</span></span>
  * <span data-ttu-id="c5e8e-445">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="c5e8e-445">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="c5e8e-446">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-446">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="c5e8e-447">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c5e8e-447">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="c5e8e-448">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="c5e8e-448">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c5e8e-449">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="c5e8e-449">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c5e8e-450">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-450">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="c5e8e-451">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-451">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="c5e8e-452">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-452">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="c5e8e-453">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-453">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="c5e8e-454">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-454">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c5e8e-455">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-455">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="c5e8e-456">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-456">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c5e8e-457">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="c5e8e-457">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c5e8e-458">可以单独使用 Kestrel，也可以将其与反向代理服务器  （如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-458">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c5e8e-459">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-459">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c5e8e-460">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-460">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c5e8e-462">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-462">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c5e8e-464">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-464">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c5e8e-465">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-465">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c5e8e-466">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-466">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c5e8e-467">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-467">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c5e8e-468">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-468">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c5e8e-469">反向代理：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-469">A reverse proxy:</span></span>

* <span data-ttu-id="c5e8e-470">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-470">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c5e8e-471">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-471">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c5e8e-472">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-472">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c5e8e-473">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-473">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c5e8e-474">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-474">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c5e8e-475">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-475">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c5e8e-476">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="c5e8e-476">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c5e8e-477">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-477">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c5e8e-478">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-478">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c5e8e-479">在 Program.cs  中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-479">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="c5e8e-480">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”  部分。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-480">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="c5e8e-481">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-481">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="c5e8e-482">如果应用未调用 `CreateDefaultBuilder` 来设置主机，请在调用 `ConfigureKestrel` 之前  先调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-482">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="c5e8e-483">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="c5e8e-483">Kestrel options</span></span>

<span data-ttu-id="c5e8e-484">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-484">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c5e8e-485">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-485">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c5e8e-486">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-486">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c5e8e-487">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-487">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c5e8e-488">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-488">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c5e8e-489">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置   ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-489">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="c5e8e-490">使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-490">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c5e8e-491">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-491">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c5e8e-492">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-492">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c5e8e-493">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-493">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c5e8e-494">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-494">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="c5e8e-495">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-495">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c5e8e-496">在 Program.cs  中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-496">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="c5e8e-497">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-497">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c5e8e-498">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="c5e8e-498">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c5e8e-499">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-499">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c5e8e-500">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-500">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="c5e8e-501">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="c5e8e-501">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c5e8e-502">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-502">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="c5e8e-503">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-503">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c5e8e-504">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-504">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="c5e8e-505">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-505">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c5e8e-506">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-506">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c5e8e-507">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-507">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c5e8e-508">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-508">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c5e8e-509">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-509">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="c5e8e-510">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-510">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c5e8e-511">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-511">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c5e8e-512">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-512">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c5e8e-513">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-513">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c5e8e-514">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="c5e8e-514">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c5e8e-515">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-515">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c5e8e-516">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-516">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c5e8e-517">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-517">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c5e8e-518">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-518">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c5e8e-519">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-519">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c5e8e-520">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-520">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c5e8e-521">以下示例演示如何在 Program.cs 中配置最小数据速率  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-521">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="c5e8e-522">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-522">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="c5e8e-523">由于协议支持请求多路复用，HTTP/2 不支持基于每个请求修改速率限制，因此 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的速率特性。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-523">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="c5e8e-524">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-524">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="c5e8e-525">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="c5e8e-525">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c5e8e-526">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-526">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c5e8e-527">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-527">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="c5e8e-528">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="c5e8e-528">Maximum streams per connection</span></span>

<span data-ttu-id="c5e8e-529">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-529">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="c5e8e-530">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-530">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="c5e8e-531">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-531">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="c5e8e-532">标题表大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-532">Header table size</span></span>

<span data-ttu-id="c5e8e-533">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-533">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="c5e8e-534">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-534">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="c5e8e-535">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-535">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="c5e8e-536">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-536">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="c5e8e-537">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-537">Maximum frame size</span></span>

<span data-ttu-id="c5e8e-538">`Http2.MaxFrameSize` 指示要接收的 HTTP/2 连接帧有效负载的最大大小。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-538">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="c5e8e-539">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-539">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="c5e8e-540">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-540">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="c5e8e-541">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-541">Maximum request header size</span></span>

<span data-ttu-id="c5e8e-542">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-542">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="c5e8e-543">此限制同时适用于压缩和未压缩表示形式中的名称和值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-543">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="c5e8e-544">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-544">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="c5e8e-545">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-545">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="c5e8e-546">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-546">Initial connection window size</span></span>

<span data-ttu-id="c5e8e-547">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-547">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="c5e8e-548">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-548">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c5e8e-549">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-549">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="c5e8e-550">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-550">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="c5e8e-551">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-551">Initial stream window size</span></span>

<span data-ttu-id="c5e8e-552">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-552">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="c5e8e-553">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-553">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="c5e8e-554">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-554">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="c5e8e-555">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-555">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="c5e8e-556">同步 IO</span><span class="sxs-lookup"><span data-stu-id="c5e8e-556">Synchronous IO</span></span>

<span data-ttu-id="c5e8e-557"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 IO。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-557"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="c5e8e-558">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-558">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="c5e8e-559">大量的阻止同步 IO 操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-559">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c5e8e-560">仅在使用不支持异步 IO 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-560">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="c5e8e-561">下面的示例启用同步 IO：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-561">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="c5e8e-562">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-562">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c5e8e-563">终结点配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-563">Endpoint configuration</span></span>

<span data-ttu-id="c5e8e-564">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-564">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c5e8e-565">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-565">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c5e8e-566">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-566">Specify URLs using the:</span></span>

* <span data-ttu-id="c5e8e-567">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-567">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c5e8e-568">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-568">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c5e8e-569">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-569">`urls` host configuration key.</span></span>
* <span data-ttu-id="c5e8e-570">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-570">`UseUrls` extension method.</span></span>

<span data-ttu-id="c5e8e-571">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-571">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c5e8e-572">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000; http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-572">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c5e8e-573">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-573">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c5e8e-574">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-574">A development certificate is created:</span></span>

* <span data-ttu-id="c5e8e-575">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-575">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c5e8e-576">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-576">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c5e8e-577">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-577">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c5e8e-578">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-578">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c5e8e-579">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-579">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c5e8e-580">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-580">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c5e8e-581">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-581">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c5e8e-582">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="c5e8e-582">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c5e8e-583">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-583">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c5e8e-584">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-584">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="c5e8e-585">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。 </span><span class="sxs-lookup"><span data-stu-id="c5e8e-585">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c5e8e-586">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="c5e8e-586">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c5e8e-587">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-587">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c5e8e-588">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-588">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="c5e8e-589">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。 </span><span class="sxs-lookup"><span data-stu-id="c5e8e-589">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="c5e8e-590">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c5e8e-590">Configure(IConfiguration)</span></span>

<span data-ttu-id="c5e8e-591">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-591">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c5e8e-592">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-592">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c5e8e-593">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c5e8e-593">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c5e8e-594">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-594">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c5e8e-595">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-595">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c5e8e-596">`UseHttps` &ndash; 将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-596">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c5e8e-597">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-597">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="c5e8e-598">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-598">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c5e8e-599">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-599">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c5e8e-600">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-600">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c5e8e-601">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-601">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c5e8e-602">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-602">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c5e8e-603">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-603">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c5e8e-604">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-604">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c5e8e-605">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-605">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c5e8e-606">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-606">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c5e8e-607">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-607">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c5e8e-608">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-608">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c5e8e-609">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-609">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c5e8e-610">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-610">Supported configurations described next:</span></span>

* <span data-ttu-id="c5e8e-611">无配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-611">No configuration</span></span>
* <span data-ttu-id="c5e8e-612">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="c5e8e-612">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c5e8e-613">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="c5e8e-613">Change the defaults in code</span></span>

<span data-ttu-id="c5e8e-614">*无配置*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-614">*No configuration*</span></span>

<span data-ttu-id="c5e8e-615">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-615">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c5e8e-616">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-616">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c5e8e-617">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-617">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c5e8e-618">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-618">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c5e8e-619">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-619">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c5e8e-620">在以下 appsettings.json 示例中  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-620">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="c5e8e-621">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）  。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-621">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c5e8e-622">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书    。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-622">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="c5e8e-623">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书   。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-623">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c5e8e-624">例如，可将 Certificates > Default 证书指定为   ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-624">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c5e8e-625">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-625">Schema notes:</span></span>

* <span data-ttu-id="c5e8e-626">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-626">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c5e8e-627">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-627">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c5e8e-628">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-628">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c5e8e-629">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-629">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c5e8e-630">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-630">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c5e8e-631">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-631">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c5e8e-632">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-632">The `Certificate` section is optional.</span></span> <span data-ttu-id="c5e8e-633">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-633">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c5e8e-634">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-634">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c5e8e-635">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书     。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-635">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c5e8e-636">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-636">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c5e8e-637">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-637">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="c5e8e-638">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-638">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c5e8e-639">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-639">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c5e8e-640">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-640">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c5e8e-641">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-641">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c5e8e-642">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-642">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c5e8e-643">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-643">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c5e8e-644">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-644">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c5e8e-645">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-645">*Change the defaults in code*</span></span>

<span data-ttu-id="c5e8e-646">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-646">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c5e8e-647">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-647">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="c5e8e-648">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-648">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c5e8e-649">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-649">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c5e8e-650">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-650">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c5e8e-651">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-651">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c5e8e-652">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-652">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c5e8e-653">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-653">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c5e8e-654">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-654">SNI support requires:</span></span>

* <span data-ttu-id="c5e8e-655">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-655">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c5e8e-656">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-656">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c5e8e-657">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-657">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c5e8e-658">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-658">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c5e8e-659">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-659">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="c5e8e-660">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="c5e8e-660">Connection logging</span></span>

<span data-ttu-id="c5e8e-661">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-661">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c5e8e-662">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-662">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c5e8e-663">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-663">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c5e8e-664">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-664">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c5e8e-665">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="c5e8e-665">Bind to a TCP socket</span></span>

<span data-ttu-id="c5e8e-666"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-666">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="c5e8e-667">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-667">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c5e8e-668">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-668">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c5e8e-669">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="c5e8e-669">Bind to a Unix socket</span></span>

<span data-ttu-id="c5e8e-670">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-670">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="c5e8e-671">端口 0</span><span class="sxs-lookup"><span data-stu-id="c5e8e-671">Port 0</span></span>

<span data-ttu-id="c5e8e-672">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-672">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c5e8e-673">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-673">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c5e8e-674">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-674">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c5e8e-675">限制</span><span class="sxs-lookup"><span data-stu-id="c5e8e-675">Limitations</span></span>

<span data-ttu-id="c5e8e-676">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-676">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c5e8e-677">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="c5e8e-677">`--urls` command-line argument</span></span>
* <span data-ttu-id="c5e8e-678">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="c5e8e-678">`urls` host configuration key</span></span>
* <span data-ttu-id="c5e8e-679">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="c5e8e-679">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c5e8e-680">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-680">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c5e8e-681">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-681">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c5e8e-682">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-682">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c5e8e-683">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-683">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c5e8e-684">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-684">IIS endpoint configuration</span></span>

<span data-ttu-id="c5e8e-685">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-685">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c5e8e-686">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-686">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="c5e8e-687">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="c5e8e-687">ListenOptions.Protocols</span></span>

<span data-ttu-id="c5e8e-688">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-688">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="c5e8e-689">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-689">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="c5e8e-690">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="c5e8e-690">`HttpProtocols` enum value</span></span> | <span data-ttu-id="c5e8e-691">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="c5e8e-691">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="c5e8e-692">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-692">HTTP/1.1 only.</span></span> <span data-ttu-id="c5e8e-693">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-693">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="c5e8e-694">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-694">HTTP/2 only.</span></span> <span data-ttu-id="c5e8e-695">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-695">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="c5e8e-696">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-696">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="c5e8e-697">HTTP/2 需要 TLS 和[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-697">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="c5e8e-698">默认协议是 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-698">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="c5e8e-699">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-699">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="c5e8e-700">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c5e8e-700">TLS version 1.2 or later</span></span>
* <span data-ttu-id="c5e8e-701">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="c5e8e-701">Renegotiation disabled</span></span>
* <span data-ttu-id="c5e8e-702">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="c5e8e-702">Compression disabled</span></span>
* <span data-ttu-id="c5e8e-703">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-703">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="c5e8e-704">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 最小 224 位</span><span class="sxs-lookup"><span data-stu-id="c5e8e-704">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="c5e8e-705">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="c5e8e-705">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="c5e8e-706">密码套件未列入阻止列表</span><span class="sxs-lookup"><span data-stu-id="c5e8e-706">Cipher suite not blacklisted</span></span>

<span data-ttu-id="c5e8e-707">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-707">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="c5e8e-708">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-708">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="c5e8e-709">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-709">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="c5e8e-710">（可选）创建 `IConnectionAdapter` 实现，以针对特定密码的每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-710">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="c5e8e-711">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-711">*Set the protocol from configuration*</span></span>

<span data-ttu-id="c5e8e-712"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-712"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="c5e8e-713">在以下 appsettings.json  示例中，为 Kestrel 的所有终结点建立默认的连接协议（HTTP/1.1 和 HTTP/2）：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-713">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="c5e8e-714">以下配置文件示例为特定终结点建立了连接协议：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-714">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="c5e8e-715">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-715">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="c5e8e-716">传输配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-716">Transport configuration</span></span>

<span data-ttu-id="c5e8e-717">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-717">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="c5e8e-718">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-718">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="c5e8e-719">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-719">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="c5e8e-720">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="c5e8e-720">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="c5e8e-721">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-721">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="c5e8e-722">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-722">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="c5e8e-723">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-723">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="c5e8e-724">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="c5e8e-724">URL prefixes</span></span>

<span data-ttu-id="c5e8e-725">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-725">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c5e8e-726">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-726">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c5e8e-727">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-727">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c5e8e-728">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="c5e8e-728">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c5e8e-729">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-729">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c5e8e-730">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="c5e8e-730">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c5e8e-731">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-731">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c5e8e-732">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="c5e8e-732">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c5e8e-733">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-733">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c5e8e-734">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-734">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c5e8e-735">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-735">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c5e8e-736">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-736">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c5e8e-737">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="c5e8e-737">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c5e8e-738">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-738">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c5e8e-739">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-739">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c5e8e-740">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-740">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c5e8e-741">主机筛选</span><span class="sxs-lookup"><span data-stu-id="c5e8e-741">Host filtering</span></span>

<span data-ttu-id="c5e8e-742">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-742">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c5e8e-743">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-743">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c5e8e-744">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-744">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c5e8e-745">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-745">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c5e8e-746">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-746">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c5e8e-747">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-747">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="c5e8e-748">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-748">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c5e8e-749">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-749">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c5e8e-750">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键   。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-750">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="c5e8e-751">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-751">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c5e8e-752">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-752">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c5e8e-753">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-753">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c5e8e-754">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-754">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c5e8e-755">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-755">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c5e8e-756">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-756">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c5e8e-757">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-757">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c5e8e-758">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-758">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c5e8e-759">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-759">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="c5e8e-760">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-760">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="c5e8e-761">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c5e8e-761">HTTPS</span></span>
* <span data-ttu-id="c5e8e-762">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="c5e8e-762">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="c5e8e-763">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="c5e8e-763">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="c5e8e-764">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-764">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="c5e8e-765">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-765">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c5e8e-766">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="c5e8e-766">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c5e8e-767">可以单独使用 Kestrel，也可以将其与反向代理服务器  （如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-767">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c5e8e-768">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-768">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c5e8e-769">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-769">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="c5e8e-771">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-771">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="c5e8e-773">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-773">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c5e8e-774">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-774">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="c5e8e-775">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-775">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c5e8e-776">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-776">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c5e8e-777">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-777">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c5e8e-778">反向代理：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-778">A reverse proxy:</span></span>

* <span data-ttu-id="c5e8e-779">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-779">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c5e8e-780">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-780">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c5e8e-781">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-781">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c5e8e-782">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-782">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c5e8e-783">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-783">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c5e8e-784">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-784">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="c5e8e-785">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="c5e8e-785">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="c5e8e-786">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-786">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c5e8e-787">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-787">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="c5e8e-788">在 Program.cs  中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-788">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="c5e8e-789">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-789">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="c5e8e-790">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”  部分。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-790">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="c5e8e-791">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="c5e8e-791">Kestrel options</span></span>

<span data-ttu-id="c5e8e-792">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-792">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="c5e8e-793">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-793">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="c5e8e-794">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-794">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="c5e8e-795">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-795">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="c5e8e-796">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-796">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="c5e8e-797">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置   ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-797">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="c5e8e-798">使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-798">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="c5e8e-799">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-799">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="c5e8e-800">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-800">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="c5e8e-801">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-801">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="c5e8e-802">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-802">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="c5e8e-803">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-803">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="c5e8e-804">在 Program.cs  中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-804">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="c5e8e-805">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-805">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="c5e8e-806">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="c5e8e-806">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="c5e8e-807">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-807">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="c5e8e-808">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-808">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="c5e8e-809">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="c5e8e-809">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="c5e8e-810">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-810">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="c5e8e-811">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-811">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="c5e8e-812">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-812">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="c5e8e-813">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-813">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="c5e8e-814">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="c5e8e-814">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="c5e8e-815">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-815">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="c5e8e-816">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-816">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c5e8e-817">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-817">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="c5e8e-818">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-818">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="c5e8e-819">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-819">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="c5e8e-820">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-820">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c5e8e-821">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-821">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="c5e8e-822">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="c5e8e-822">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="c5e8e-823">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-823">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="c5e8e-824">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-824">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="c5e8e-825">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-825">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="c5e8e-826">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-826">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="c5e8e-827">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-827">A minimum rate also applies to the response.</span></span> <span data-ttu-id="c5e8e-828">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-828">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="c5e8e-829">以下示例演示如何在 Program.cs 中配置最小数据速率  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-829">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="c5e8e-830">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="c5e8e-830">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="c5e8e-831">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-831">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="c5e8e-832">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-832">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="c5e8e-833">同步 IO</span><span class="sxs-lookup"><span data-stu-id="c5e8e-833">Synchronous IO</span></span>

<span data-ttu-id="c5e8e-834"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 IO。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-834"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="c5e8e-835">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-835">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="c5e8e-836">大量的阻止同步 IO 操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-836">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="c5e8e-837">仅在使用不支持异步 IO 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-837">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="c5e8e-838">下面的示例禁用同步 IO：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-838">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="c5e8e-839">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-839">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="c5e8e-840">终结点配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-840">Endpoint configuration</span></span>

<span data-ttu-id="c5e8e-841">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-841">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="c5e8e-842">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-842">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="c5e8e-843">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-843">Specify URLs using the:</span></span>

* <span data-ttu-id="c5e8e-844">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-844">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="c5e8e-845">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-845">`--urls` command-line argument.</span></span>
* <span data-ttu-id="c5e8e-846">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-846">`urls` host configuration key.</span></span>
* <span data-ttu-id="c5e8e-847">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-847">`UseUrls` extension method.</span></span>

<span data-ttu-id="c5e8e-848">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-848">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="c5e8e-849">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000; http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-849">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="c5e8e-850">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-850">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="c5e8e-851">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-851">A development certificate is created:</span></span>

* <span data-ttu-id="c5e8e-852">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-852">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="c5e8e-853">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-853">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="c5e8e-854">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-854">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="c5e8e-855">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-855">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c5e8e-856">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-856">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="c5e8e-857">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-857">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="c5e8e-858">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-858">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="c5e8e-859">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="c5e8e-859">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="c5e8e-860">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-860">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="c5e8e-861">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-861">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="c5e8e-862">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。 </span><span class="sxs-lookup"><span data-stu-id="c5e8e-862">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="c5e8e-863">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="c5e8e-863">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="c5e8e-864">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-864">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="c5e8e-865">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-865">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="c5e8e-866">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。 </span><span class="sxs-lookup"><span data-stu-id="c5e8e-866">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="c5e8e-867">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="c5e8e-867">Configure(IConfiguration)</span></span>

<span data-ttu-id="c5e8e-868">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-868">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="c5e8e-869">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-869">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="c5e8e-870">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="c5e8e-870">ListenOptions.UseHttps</span></span>

<span data-ttu-id="c5e8e-871">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-871">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="c5e8e-872">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-872">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="c5e8e-873">`UseHttps` &ndash; 将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-873">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="c5e8e-874">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-874">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="c5e8e-875">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-875">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="c5e8e-876">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-876">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="c5e8e-877">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-877">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="c5e8e-878">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-878">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="c5e8e-879">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-879">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="c5e8e-880">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-880">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="c5e8e-881">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-881">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="c5e8e-882">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-882">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="c5e8e-883">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-883">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="c5e8e-884">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-884">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="c5e8e-885">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-885">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c5e8e-886">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-886">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="c5e8e-887">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-887">Supported configurations described next:</span></span>

* <span data-ttu-id="c5e8e-888">无配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-888">No configuration</span></span>
* <span data-ttu-id="c5e8e-889">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="c5e8e-889">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="c5e8e-890">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="c5e8e-890">Change the defaults in code</span></span>

<span data-ttu-id="c5e8e-891">*无配置*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-891">*No configuration*</span></span>

<span data-ttu-id="c5e8e-892">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-892">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="c5e8e-893">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-893">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="c5e8e-894">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-894">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="c5e8e-895">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-895">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="c5e8e-896">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-896">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="c5e8e-897">在以下 appsettings.json 示例中  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-897">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="c5e8e-898">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）  。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-898">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="c5e8e-899">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书    。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-899">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="c5e8e-900">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书   。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-900">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="c5e8e-901">例如，可将 Certificates > Default 证书指定为   ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-901">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="c5e8e-902">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-902">Schema notes:</span></span>

* <span data-ttu-id="c5e8e-903">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-903">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="c5e8e-904">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-904">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="c5e8e-905">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-905">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="c5e8e-906">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-906">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="c5e8e-907">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-907">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="c5e8e-908">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-908">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="c5e8e-909">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-909">The `Certificate` section is optional.</span></span> <span data-ttu-id="c5e8e-910">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-910">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="c5e8e-911">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-911">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="c5e8e-912">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书     。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-912">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="c5e8e-913">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-913">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="c5e8e-914">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-914">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="c5e8e-915">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-915">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="c5e8e-916">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-916">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="c5e8e-917">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-917">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="c5e8e-918">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-918">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="c5e8e-919">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-919">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="c5e8e-920">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-920">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="c5e8e-921">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-921">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="c5e8e-922">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-922">*Change the defaults in code*</span></span>

<span data-ttu-id="c5e8e-923">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-923">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="c5e8e-924">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-924">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="c5e8e-925">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="c5e8e-925">*Kestrel support for SNI*</span></span>

<span data-ttu-id="c5e8e-926">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-926">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="c5e8e-927">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-927">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="c5e8e-928">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-928">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="c5e8e-929">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-929">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="c5e8e-930">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-930">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="c5e8e-931">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-931">SNI support requires:</span></span>

* <span data-ttu-id="c5e8e-932">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-932">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="c5e8e-933">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-933">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="c5e8e-934">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-934">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="c5e8e-935">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-935">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="c5e8e-936">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-936">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="c5e8e-937">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="c5e8e-937">Connection logging</span></span>

<span data-ttu-id="c5e8e-938">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-938">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="c5e8e-939">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-939">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="c5e8e-940">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-940">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="c5e8e-941">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-941">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="c5e8e-942">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="c5e8e-942">Bind to a TCP socket</span></span>

<span data-ttu-id="c5e8e-943"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-943">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="c5e8e-944">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-944">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="c5e8e-945">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-945">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="c5e8e-946">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="c5e8e-946">Bind to a Unix socket</span></span>

<span data-ttu-id="c5e8e-947">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-947">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

### <a name="port-0"></a><span data-ttu-id="c5e8e-948">端口 0</span><span class="sxs-lookup"><span data-stu-id="c5e8e-948">Port 0</span></span>

<span data-ttu-id="c5e8e-949">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-949">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="c5e8e-950">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-950">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="c5e8e-951">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-951">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="c5e8e-952">限制</span><span class="sxs-lookup"><span data-stu-id="c5e8e-952">Limitations</span></span>

<span data-ttu-id="c5e8e-953">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-953">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="c5e8e-954">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="c5e8e-954">`--urls` command-line argument</span></span>
* <span data-ttu-id="c5e8e-955">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="c5e8e-955">`urls` host configuration key</span></span>
* <span data-ttu-id="c5e8e-956">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="c5e8e-956">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="c5e8e-957">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-957">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="c5e8e-958">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-958">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="c5e8e-959">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-959">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="c5e8e-960">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-960">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="c5e8e-961">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-961">IIS endpoint configuration</span></span>

<span data-ttu-id="c5e8e-962">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-962">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="c5e8e-963">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-963">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="c5e8e-964">传输配置</span><span class="sxs-lookup"><span data-stu-id="c5e8e-964">Transport configuration</span></span>

<span data-ttu-id="c5e8e-965">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-965">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="c5e8e-966">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-966">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="c5e8e-967">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-967">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="c5e8e-968">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="c5e8e-968">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="c5e8e-969">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-969">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="c5e8e-970">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-970">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="c5e8e-971">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-971">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="c5e8e-972">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="c5e8e-972">URL prefixes</span></span>

<span data-ttu-id="c5e8e-973">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-973">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="c5e8e-974">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-974">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="c5e8e-975">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-975">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="c5e8e-976">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="c5e8e-976">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="c5e8e-977">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-977">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="c5e8e-978">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="c5e8e-978">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="c5e8e-979">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-979">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="c5e8e-980">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="c5e8e-980">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="c5e8e-981">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-981">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="c5e8e-982">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-982">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="c5e8e-983">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-983">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="c5e8e-984">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-984">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="c5e8e-985">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="c5e8e-985">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="c5e8e-986">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-986">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="c5e8e-987">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-987">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="c5e8e-988">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-988">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="c5e8e-989">主机筛选</span><span class="sxs-lookup"><span data-stu-id="c5e8e-989">Host filtering</span></span>

<span data-ttu-id="c5e8e-990">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-990">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="c5e8e-991">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-991">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="c5e8e-992">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-992">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="c5e8e-993">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-993">`Host` headers aren't validated.</span></span>

<span data-ttu-id="c5e8e-994">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-994">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="c5e8e-995">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-995">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="c5e8e-996">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-996">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="c5e8e-997">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-997">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="c5e8e-998">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键   。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-998">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="c5e8e-999">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-999">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="c5e8e-1000">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="c5e8e-1000">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="c5e8e-1001">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-1001">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="c5e8e-1002">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-1002">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="c5e8e-1003">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-1003">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="c5e8e-1004">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-1004">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="c5e8e-1005">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="c5e8e-1005">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c5e8e-1006">其他资源</span><span class="sxs-lookup"><span data-stu-id="c5e8e-1006">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="c5e8e-1007">RFC 7230：消息语法和路由（5.4 节：主机）</span><span class="sxs-lookup"><span data-stu-id="c5e8e-1007">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
