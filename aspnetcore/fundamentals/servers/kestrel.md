---
title: ASP.NET Core 中的 Kestrel Web 服务器实现
author: guardrex
description: 了解跨平台 ASP.NET Core Web 服务器 Kestrel。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/servers/kestrel
ms.openlocfilehash: d026e1b6fc1a9ecc66014eacb8eb0b46dd9353ec
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171722"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="f0718-103">ASP.NET Core 中的 Kestrel Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="f0718-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="f0718-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)[Stephen Halter](https://twitter.com/halter73) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f0718-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0718-105">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="f0718-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="f0718-106">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="f0718-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="f0718-107">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="f0718-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="f0718-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="f0718-108">HTTPS</span></span>
* <span data-ttu-id="f0718-109">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="f0718-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="f0718-110">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="f0718-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="f0718-111">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="f0718-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="f0718-112">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f0718-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="f0718-113">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="f0718-114">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f0718-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="f0718-115">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="f0718-115">HTTP/2 support</span></span>

<span data-ttu-id="f0718-116">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="f0718-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="f0718-117">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="f0718-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="f0718-118">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="f0718-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="f0718-119">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="f0718-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="f0718-120">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f0718-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="f0718-121">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="f0718-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="f0718-122">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="f0718-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="f0718-123">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f0718-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="f0718-124">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="f0718-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="f0718-125">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="f0718-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="f0718-126">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="f0718-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="f0718-127">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="f0718-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="f0718-128">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f0718-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="f0718-129">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="f0718-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="f0718-130">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="f0718-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="f0718-131">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="f0718-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="f0718-132">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="f0718-133">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="f0718-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="f0718-135">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="f0718-137">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="f0718-138">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="f0718-139">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="f0718-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="f0718-140">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="f0718-141">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="f0718-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="f0718-142">反向代理：</span><span class="sxs-lookup"><span data-stu-id="f0718-142">A reverse proxy:</span></span>

* <span data-ttu-id="f0718-143">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="f0718-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="f0718-144">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="f0718-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="f0718-145">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="f0718-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="f0718-146">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="f0718-147">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="f0718-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="f0718-148">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="f0718-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="f0718-149">ASP.NET Core 应用中的 Kestrel</span><span class="sxs-lookup"><span data-stu-id="f0718-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="f0718-150">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="f0718-151">在“Program.cs”中，<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="f0718-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="f0718-152">有关生成主机的详细信息，请参阅 <xref:fundamentals/host/generic-host#set-up-a-host> 的“设置主机”和“默认生成器设置”部分。</span><span class="sxs-lookup"><span data-stu-id="f0718-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="f0718-153">若要在调用 `ConfigureWebHostDefaults` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="f0718-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="f0718-154">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="f0718-154">Kestrel options</span></span>

<span data-ttu-id="f0718-155">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="f0718-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="f0718-156">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="f0718-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="f0718-157">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="f0718-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="f0718-158">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="f0718-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="f0718-159">在本文后面的示例中，Kestrel 选项是采用 C# 代码配置的。</span><span class="sxs-lookup"><span data-stu-id="f0718-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="f0718-160">还可以使用 [配置提供程序](xref:fundamentals/configuration/index)设置 Kestrel 选项。</span><span class="sxs-lookup"><span data-stu-id="f0718-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="f0718-161">例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="f0718-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 和 [终结点配置](#endpoint-configuration) 可以通过配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="f0718-163">其余的 Kestrel 配置必须采用 C# 代码进行配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="f0718-164">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f0718-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="f0718-165">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="f0718-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="f0718-166">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="f0718-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="f0718-167">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="f0718-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="f0718-168">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="f0718-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="f0718-169">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="f0718-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="f0718-170">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="f0718-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="f0718-171">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="f0718-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="f0718-172">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="f0718-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="f0718-173">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="f0718-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="f0718-174">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="f0718-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="f0718-175">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="f0718-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="f0718-176">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="f0718-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="f0718-177">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="f0718-178">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="f0718-179">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="f0718-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="f0718-180">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="f0718-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="f0718-181">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="f0718-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="f0718-182">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="f0718-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="f0718-183">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="f0718-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="f0718-184">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="f0718-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="f0718-185">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="f0718-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="f0718-186">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="f0718-187">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="f0718-188">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="f0718-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="f0718-189">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="f0718-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="f0718-190">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="f0718-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="f0718-191">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="f0718-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="f0718-192">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="f0718-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="f0718-193">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="f0718-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="f0718-194">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="f0718-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="f0718-195">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="f0718-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="f0718-196">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="f0718-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="f0718-197">用于 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>，因为鉴于协议支持请求多路复用，HTTP/2 通常不支持按请求修改速率限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="f0718-198">不过，用于 HTTP/2 请求的 `HttpContext.Features` 中仍存在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>，因为仍可以通过将 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 设置为 `null`（甚至对于 HTTP/2 请求），按请求完全禁用读取速率限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="f0718-199">对于给定 HTTP/2 请求，尝试读取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或尝试将它设置为除 `null` 以外的值会导致 `NotSupportedException` 抛出。</span><span class="sxs-lookup"><span data-stu-id="f0718-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="f0718-200">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="f0718-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="f0718-201">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="f0718-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="f0718-202">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="f0718-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="f0718-203">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="f0718-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="f0718-204">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="f0718-204">Maximum streams per connection</span></span>

<span data-ttu-id="f0718-205">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="f0718-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="f0718-206">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="f0718-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="f0718-207">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="f0718-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="f0718-208">标题表大小</span><span class="sxs-lookup"><span data-stu-id="f0718-208">Header table size</span></span>

<span data-ttu-id="f0718-209">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="f0718-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="f0718-210">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="f0718-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="f0718-211">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="f0718-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="f0718-212">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="f0718-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="f0718-213">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="f0718-213">Maximum frame size</span></span>

<span data-ttu-id="f0718-214">`Http2.MaxFrameSize` 表示服务器接收或发送的 HTTP/2 连接帧有效负载的最大允许大小。</span><span class="sxs-lookup"><span data-stu-id="f0718-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="f0718-215">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="f0718-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="f0718-216">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="f0718-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="f0718-217">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="f0718-217">Maximum request header size</span></span>

<span data-ttu-id="f0718-218">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="f0718-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="f0718-219">此限制适用于名称和值的压缩和未压缩表示形式。</span><span class="sxs-lookup"><span data-stu-id="f0718-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="f0718-220">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="f0718-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="f0718-221">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="f0718-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="f0718-222">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="f0718-222">Initial connection window size</span></span>

<span data-ttu-id="f0718-223">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f0718-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="f0718-224">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="f0718-225">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="f0718-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="f0718-226">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="f0718-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="f0718-227">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="f0718-227">Initial stream window size</span></span>

<span data-ttu-id="f0718-228">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f0718-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="f0718-229">请求也受 `Http2.InitialConnectionWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="f0718-230">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="f0718-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="f0718-231">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="f0718-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="f0718-232">同步 IO</span><span class="sxs-lookup"><span data-stu-id="f0718-232">Synchronous IO</span></span>

<span data-ttu-id="f0718-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 IO。</span><span class="sxs-lookup"><span data-stu-id="f0718-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="f0718-234">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="f0718-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="f0718-235">大量的阻止同步 IO 操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="f0718-235">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="f0718-236">仅在使用不支持异步 IO 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="f0718-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="f0718-237">下面的示例启用同步 IO：</span><span class="sxs-lookup"><span data-stu-id="f0718-237">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="f0718-238">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="f0718-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="f0718-239">终结点配置</span><span class="sxs-lookup"><span data-stu-id="f0718-239">Endpoint configuration</span></span>

<span data-ttu-id="f0718-240">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="f0718-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="f0718-241">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="f0718-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="f0718-242">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="f0718-242">Specify URLs using the:</span></span>

* <span data-ttu-id="f0718-243">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="f0718-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="f0718-244">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="f0718-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="f0718-245">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="f0718-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="f0718-246">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="f0718-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="f0718-247">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="f0718-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="f0718-248">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="f0718-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="f0718-249">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="f0718-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="f0718-250">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="f0718-250">A development certificate is created:</span></span>

* <span data-ttu-id="f0718-251">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="f0718-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="f0718-252">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="f0718-253">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="f0718-254">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="f0718-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f0718-255">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="f0718-256">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="f0718-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="f0718-257">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="f0718-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="f0718-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="f0718-259">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="f0718-260">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f0718-261">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="f0718-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="f0718-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="f0718-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="f0718-263">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="f0718-264">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f0718-265">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="f0718-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="f0718-266">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="f0718-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="f0718-267">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="f0718-268">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="f0718-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="f0718-269">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="f0718-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="f0718-270">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="f0718-271">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="f0718-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="f0718-272">`UseHttps` &ndash; 将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-272">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="f0718-273">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="f0718-273">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="f0718-274">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="f0718-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="f0718-275">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="f0718-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="f0718-276">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="f0718-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="f0718-277">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="f0718-278">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="f0718-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="f0718-279">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="f0718-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="f0718-280">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="f0718-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="f0718-281">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="f0718-282">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="f0718-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="f0718-283">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="f0718-284">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="f0718-285">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="f0718-286">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-286">Supported configurations described next:</span></span>

* <span data-ttu-id="f0718-287">无配置</span><span class="sxs-lookup"><span data-stu-id="f0718-287">No configuration</span></span>
* <span data-ttu-id="f0718-288">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="f0718-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="f0718-289">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="f0718-289">Change the defaults in code</span></span>

<span data-ttu-id="f0718-290">*无配置*</span><span class="sxs-lookup"><span data-stu-id="f0718-290">*No configuration*</span></span>

<span data-ttu-id="f0718-291">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="f0718-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="f0718-292">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="f0718-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="f0718-293">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="f0718-294">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="f0718-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="f0718-295">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="f0718-296">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="f0718-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="f0718-297">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="f0718-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="f0718-298">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="f0718-299">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="f0718-300">例如，可将 Certificates > Default 证书指定为：</span><span class="sxs-lookup"><span data-stu-id="f0718-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="f0718-301">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="f0718-301">Schema notes:</span></span>

* <span data-ttu-id="f0718-302">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="f0718-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="f0718-303">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="f0718-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="f0718-304">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="f0718-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="f0718-305">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="f0718-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="f0718-306">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="f0718-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="f0718-307">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="f0718-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="f0718-308">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="f0718-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="f0718-309">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="f0718-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="f0718-310">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="f0718-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="f0718-311">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="f0718-312">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="f0718-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="f0718-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="f0718-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="f0718-314">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="f0718-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="f0718-315">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="f0718-316">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="f0718-317">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="f0718-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="f0718-318">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="f0718-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="f0718-319">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="f0718-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="f0718-320">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="f0718-321">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="f0718-321">*Change the defaults in code*</span></span>

<span data-ttu-id="f0718-322">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="f0718-323">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="f0718-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="f0718-324">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="f0718-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="f0718-325">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="f0718-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="f0718-326">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="f0718-327">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="f0718-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="f0718-328">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="f0718-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="f0718-329">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="f0718-330">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="f0718-330">SNI support requires:</span></span>

* <span data-ttu-id="f0718-331">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="f0718-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="f0718-332">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="f0718-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="f0718-333">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="f0718-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="f0718-334">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="f0718-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="f0718-335">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="f0718-336">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="f0718-336">Connection logging</span></span>

<span data-ttu-id="f0718-337">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="f0718-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="f0718-338">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="f0718-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="f0718-339">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="f0718-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="f0718-340">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="f0718-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="f0718-341">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="f0718-341">Bind to a TCP socket</span></span>

<span data-ttu-id="f0718-342"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="f0718-343">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="f0718-344">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="f0718-345">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="f0718-345">Bind to a Unix socket</span></span>

<span data-ttu-id="f0718-346">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="f0718-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="f0718-347">在 Nginx confiuguration 文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="f0718-347">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="f0718-348">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="f0718-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="f0718-349">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="f0718-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="f0718-350">端口 0</span><span class="sxs-lookup"><span data-stu-id="f0718-350">Port 0</span></span>

<span data-ttu-id="f0718-351">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="f0718-352">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="f0718-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="f0718-353">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="f0718-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="f0718-354">限制</span><span class="sxs-lookup"><span data-stu-id="f0718-354">Limitations</span></span>

<span data-ttu-id="f0718-355">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="f0718-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="f0718-356">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="f0718-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="f0718-357">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="f0718-357">`urls` host configuration key</span></span>
* <span data-ttu-id="f0718-358">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="f0718-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="f0718-359">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="f0718-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="f0718-360">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="f0718-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="f0718-361">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="f0718-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="f0718-362">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="f0718-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="f0718-363">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="f0718-363">IIS endpoint configuration</span></span>

<span data-ttu-id="f0718-364">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="f0718-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="f0718-365">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="f0718-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="f0718-366">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="f0718-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="f0718-367">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="f0718-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="f0718-368">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="f0718-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="f0718-369">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="f0718-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="f0718-370">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="f0718-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="f0718-371">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f0718-371">HTTP/1.1 only.</span></span> <span data-ttu-id="f0718-372">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="f0718-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="f0718-373">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f0718-373">HTTP/2 only.</span></span> <span data-ttu-id="f0718-374">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="f0718-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="f0718-375">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f0718-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="f0718-376">HTTP/2 要求客户端在 TLS [应用层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手过程中选择 HTTP/2；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f0718-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="f0718-377">任何终结点的默认 `ListenOptions.Protocols` 值都为 `HttpProtocols.Http1AndHttp2`。</span><span class="sxs-lookup"><span data-stu-id="f0718-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="f0718-378">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="f0718-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="f0718-379">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f0718-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="f0718-380">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="f0718-380">Renegotiation disabled</span></span>
* <span data-ttu-id="f0718-381">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="f0718-381">Compression disabled</span></span>
* <span data-ttu-id="f0718-382">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="f0718-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="f0718-383">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 最小 224 位</span><span class="sxs-lookup"><span data-stu-id="f0718-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="f0718-384">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="f0718-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="f0718-385">密码套件未列入阻止列表</span><span class="sxs-lookup"><span data-stu-id="f0718-385">Cipher suite not blacklisted</span></span>

<span data-ttu-id="f0718-386">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="f0718-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="f0718-387">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="f0718-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="f0718-388">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="f0718-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="f0718-389">使用连接中间件，针对特定密码的每个连接筛选 TLS 握手（如有必要）。</span><span class="sxs-lookup"><span data-stu-id="f0718-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="f0718-390">下面的示例针对应用不支持的任何密码算法引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="f0718-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="f0718-391">或者，定义 [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 并将其与可接受的密码套件列表进行比较。</span><span class="sxs-lookup"><span data-stu-id="f0718-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="f0718-392">没有哪种加密是使用 [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 密码算法。</span><span class="sxs-lookup"><span data-stu-id="f0718-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="f0718-393">连接筛选也可以通过 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda 进行配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="f0718-394">在 Linux 上，<xref:System.Net.Security.CipherSuitesPolicy> 可用于针对每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="f0718-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="f0718-395">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="f0718-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="f0718-396">`CreateDefaultBuilder` 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="f0718-397">以下 appsettings.json 示例将 HTTP/1.1 建立为所有终结点的默认连接协议：</span><span class="sxs-lookup"><span data-stu-id="f0718-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="f0718-398">以下 appsettings.json 示例将 HTTP/1.1 建立为所有指定终结点的连接协议：</span><span class="sxs-lookup"><span data-stu-id="f0718-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="f0718-399">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="f0718-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="f0718-400">传输配置</span><span class="sxs-lookup"><span data-stu-id="f0718-400">Transport configuration</span></span>

<span data-ttu-id="f0718-401">对于需要使用 Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) 的项目：</span><span class="sxs-lookup"><span data-stu-id="f0718-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="f0718-402">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="f0718-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="f0718-403">调用 `IWebHostBuilder` 上的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="f0718-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="f0718-404">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="f0718-404">URL prefixes</span></span>

<span data-ttu-id="f0718-405">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="f0718-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="f0718-406">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="f0718-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="f0718-407">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="f0718-408">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="f0718-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="f0718-409">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="f0718-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="f0718-410">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="f0718-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="f0718-411">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="f0718-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="f0718-412">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="f0718-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="f0718-413">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="f0718-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="f0718-414">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="f0718-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="f0718-415">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="f0718-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="f0718-416">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="f0718-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="f0718-417">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="f0718-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="f0718-418">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="f0718-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="f0718-419">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="f0718-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="f0718-420">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="f0718-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="f0718-421">主机筛选</span><span class="sxs-lookup"><span data-stu-id="f0718-421">Host filtering</span></span>

<span data-ttu-id="f0718-422">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="f0718-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="f0718-423">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="f0718-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="f0718-424">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f0718-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="f0718-425">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="f0718-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="f0718-426">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="f0718-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="f0718-427">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包为 ASP.NET Core 应用隐式提供。</span><span class="sxs-lookup"><span data-stu-id="f0718-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="f0718-428">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="f0718-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="f0718-429">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="f0718-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="f0718-430">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="f0718-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="f0718-431">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="f0718-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="f0718-432">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="f0718-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="f0718-433">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="f0718-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="f0718-434">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="f0718-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="f0718-435">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="f0718-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="f0718-436">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="f0718-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="f0718-437">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="f0718-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f0718-438">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="f0718-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="f0718-439">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="f0718-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="f0718-440">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="f0718-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="f0718-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="f0718-441">HTTPS</span></span>
* <span data-ttu-id="f0718-442">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="f0718-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="f0718-443">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="f0718-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="f0718-444">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="f0718-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="f0718-445">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f0718-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="f0718-446">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="f0718-447">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f0718-447">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="f0718-448">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="f0718-448">HTTP/2 support</span></span>

<span data-ttu-id="f0718-449">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="f0718-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="f0718-450">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="f0718-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="f0718-451">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="f0718-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="f0718-452">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="f0718-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="f0718-453">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f0718-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="f0718-454">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="f0718-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="f0718-455">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="f0718-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="f0718-456">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f0718-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="f0718-457">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="f0718-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="f0718-458">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="f0718-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="f0718-459">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="f0718-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="f0718-460">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="f0718-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="f0718-461">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f0718-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="f0718-462">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="f0718-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="f0718-463">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="f0718-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="f0718-464">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="f0718-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="f0718-465">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="f0718-466">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="f0718-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="f0718-468">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-468">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="f0718-470">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="f0718-471">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="f0718-472">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="f0718-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="f0718-473">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="f0718-474">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="f0718-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="f0718-475">反向代理：</span><span class="sxs-lookup"><span data-stu-id="f0718-475">A reverse proxy:</span></span>

* <span data-ttu-id="f0718-476">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="f0718-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="f0718-477">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="f0718-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="f0718-478">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="f0718-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="f0718-479">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="f0718-480">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="f0718-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="f0718-481">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="f0718-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="f0718-482">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="f0718-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="f0718-483">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="f0718-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f0718-484">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="f0718-485">在 Program.cs 中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="f0718-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="f0718-486">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”部分。</span><span class="sxs-lookup"><span data-stu-id="f0718-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="f0718-487">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="f0718-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="f0718-488">如果应用未调用 `CreateDefaultBuilder` 来设置主机，请在调用 `ConfigureKestrel` 之前先调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="f0718-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="f0718-489">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="f0718-489">Kestrel options</span></span>

<span data-ttu-id="f0718-490">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="f0718-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="f0718-491">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="f0718-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="f0718-492">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="f0718-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="f0718-493">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="f0718-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="f0718-494">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="f0718-495">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="f0718-496">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f0718-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="f0718-497">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="f0718-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="f0718-498">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="f0718-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="f0718-499">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="f0718-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="f0718-500">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="f0718-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="f0718-501">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="f0718-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="f0718-502">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="f0718-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="f0718-503">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="f0718-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="f0718-504">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="f0718-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="f0718-505">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="f0718-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="f0718-506">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="f0718-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="f0718-507">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="f0718-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="f0718-508">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="f0718-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="f0718-509">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="f0718-510">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="f0718-511">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="f0718-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="f0718-512">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="f0718-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="f0718-513">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="f0718-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="f0718-514">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="f0718-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="f0718-515">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="f0718-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="f0718-516">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="f0718-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="f0718-517">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="f0718-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="f0718-518">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="f0718-519">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="f0718-520">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="f0718-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="f0718-521">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="f0718-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="f0718-522">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="f0718-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="f0718-523">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="f0718-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="f0718-524">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="f0718-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="f0718-525">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="f0718-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="f0718-526">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="f0718-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="f0718-527">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="f0718-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="f0718-528">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="f0718-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="f0718-529">由于协议支持请求多路复用，HTTP/2 不支持基于每个请求修改速率限制，因此 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的速率特性。</span><span class="sxs-lookup"><span data-stu-id="f0718-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="f0718-530">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="f0718-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="f0718-531">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="f0718-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="f0718-532">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="f0718-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="f0718-533">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="f0718-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="f0718-534">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="f0718-534">Maximum streams per connection</span></span>

<span data-ttu-id="f0718-535">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="f0718-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="f0718-536">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="f0718-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="f0718-537">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="f0718-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="f0718-538">标题表大小</span><span class="sxs-lookup"><span data-stu-id="f0718-538">Header table size</span></span>

<span data-ttu-id="f0718-539">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="f0718-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="f0718-540">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="f0718-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="f0718-541">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="f0718-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="f0718-542">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="f0718-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="f0718-543">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="f0718-543">Maximum frame size</span></span>

<span data-ttu-id="f0718-544">`Http2.MaxFrameSize` 指示要接收的 HTTP/2 连接帧有效负载的最大大小。</span><span class="sxs-lookup"><span data-stu-id="f0718-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="f0718-545">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="f0718-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="f0718-546">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="f0718-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="f0718-547">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="f0718-547">Maximum request header size</span></span>

<span data-ttu-id="f0718-548">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="f0718-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="f0718-549">此限制同时适用于压缩和未压缩表示形式中的名称和值。</span><span class="sxs-lookup"><span data-stu-id="f0718-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="f0718-550">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="f0718-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="f0718-551">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="f0718-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="f0718-552">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="f0718-552">Initial connection window size</span></span>

<span data-ttu-id="f0718-553">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f0718-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="f0718-554">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="f0718-555">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="f0718-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="f0718-556">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="f0718-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="f0718-557">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="f0718-557">Initial stream window size</span></span>

<span data-ttu-id="f0718-558">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f0718-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="f0718-559">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="f0718-560">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="f0718-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="f0718-561">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="f0718-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="f0718-562">同步 IO</span><span class="sxs-lookup"><span data-stu-id="f0718-562">Synchronous IO</span></span>

<span data-ttu-id="f0718-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 IO。</span><span class="sxs-lookup"><span data-stu-id="f0718-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="f0718-564">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f0718-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f0718-565">大量的阻止同步 IO 操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="f0718-565">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="f0718-566">仅在使用不支持异步 IO 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="f0718-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="f0718-567">下面的示例启用同步 IO：</span><span class="sxs-lookup"><span data-stu-id="f0718-567">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="f0718-568">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="f0718-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="f0718-569">终结点配置</span><span class="sxs-lookup"><span data-stu-id="f0718-569">Endpoint configuration</span></span>

<span data-ttu-id="f0718-570">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="f0718-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="f0718-571">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="f0718-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="f0718-572">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="f0718-572">Specify URLs using the:</span></span>

* <span data-ttu-id="f0718-573">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="f0718-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="f0718-574">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="f0718-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="f0718-575">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="f0718-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="f0718-576">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="f0718-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="f0718-577">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="f0718-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="f0718-578">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="f0718-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="f0718-579">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="f0718-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="f0718-580">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="f0718-580">A development certificate is created:</span></span>

* <span data-ttu-id="f0718-581">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="f0718-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="f0718-582">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="f0718-583">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="f0718-584">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="f0718-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f0718-585">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="f0718-586">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="f0718-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="f0718-587">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="f0718-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="f0718-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="f0718-589">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="f0718-590">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f0718-591">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="f0718-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="f0718-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="f0718-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="f0718-593">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="f0718-594">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f0718-595">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="f0718-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="f0718-596">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="f0718-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="f0718-597">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="f0718-598">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="f0718-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="f0718-599">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="f0718-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="f0718-600">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="f0718-601">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="f0718-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="f0718-602">`UseHttps` &ndash; 将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-602">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="f0718-603">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="f0718-603">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="f0718-604">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="f0718-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="f0718-605">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="f0718-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="f0718-606">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="f0718-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="f0718-607">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="f0718-608">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="f0718-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="f0718-609">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="f0718-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="f0718-610">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="f0718-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="f0718-611">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="f0718-612">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="f0718-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="f0718-613">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="f0718-614">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="f0718-615">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="f0718-616">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-616">Supported configurations described next:</span></span>

* <span data-ttu-id="f0718-617">无配置</span><span class="sxs-lookup"><span data-stu-id="f0718-617">No configuration</span></span>
* <span data-ttu-id="f0718-618">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="f0718-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="f0718-619">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="f0718-619">Change the defaults in code</span></span>

<span data-ttu-id="f0718-620">*无配置*</span><span class="sxs-lookup"><span data-stu-id="f0718-620">*No configuration*</span></span>

<span data-ttu-id="f0718-621">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="f0718-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="f0718-622">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="f0718-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="f0718-623">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="f0718-624">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="f0718-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="f0718-625">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="f0718-626">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="f0718-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="f0718-627">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="f0718-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="f0718-628">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="f0718-629">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="f0718-630">例如，可将 Certificates > Default 证书指定为：</span><span class="sxs-lookup"><span data-stu-id="f0718-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="f0718-631">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="f0718-631">Schema notes:</span></span>

* <span data-ttu-id="f0718-632">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="f0718-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="f0718-633">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="f0718-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="f0718-634">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="f0718-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="f0718-635">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="f0718-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="f0718-636">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="f0718-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="f0718-637">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="f0718-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="f0718-638">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="f0718-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="f0718-639">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="f0718-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="f0718-640">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="f0718-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="f0718-641">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="f0718-642">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="f0718-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="f0718-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="f0718-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="f0718-644">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="f0718-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="f0718-645">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="f0718-646">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="f0718-647">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="f0718-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="f0718-648">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="f0718-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="f0718-649">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="f0718-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="f0718-650">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="f0718-651">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="f0718-651">*Change the defaults in code*</span></span>

<span data-ttu-id="f0718-652">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="f0718-653">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="f0718-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="f0718-654">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="f0718-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="f0718-655">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="f0718-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="f0718-656">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="f0718-657">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="f0718-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="f0718-658">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="f0718-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="f0718-659">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="f0718-660">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="f0718-660">SNI support requires:</span></span>

* <span data-ttu-id="f0718-661">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="f0718-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="f0718-662">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="f0718-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="f0718-663">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="f0718-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="f0718-664">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="f0718-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="f0718-665">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="f0718-666">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="f0718-666">Connection logging</span></span>

<span data-ttu-id="f0718-667">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="f0718-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="f0718-668">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="f0718-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="f0718-669">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="f0718-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="f0718-670">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="f0718-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="f0718-671">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="f0718-671">Bind to a TCP socket</span></span>

<span data-ttu-id="f0718-672"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="f0718-673">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="f0718-674">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="f0718-675">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="f0718-675">Bind to a Unix socket</span></span>

<span data-ttu-id="f0718-676">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="f0718-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="f0718-677">在 Nginx confiuguration 文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="f0718-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="f0718-678">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="f0718-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="f0718-679">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="f0718-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="f0718-680">端口 0</span><span class="sxs-lookup"><span data-stu-id="f0718-680">Port 0</span></span>

<span data-ttu-id="f0718-681">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="f0718-682">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="f0718-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="f0718-683">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="f0718-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="f0718-684">限制</span><span class="sxs-lookup"><span data-stu-id="f0718-684">Limitations</span></span>

<span data-ttu-id="f0718-685">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="f0718-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="f0718-686">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="f0718-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="f0718-687">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="f0718-687">`urls` host configuration key</span></span>
* <span data-ttu-id="f0718-688">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="f0718-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="f0718-689">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="f0718-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="f0718-690">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="f0718-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="f0718-691">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="f0718-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="f0718-692">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="f0718-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="f0718-693">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="f0718-693">IIS endpoint configuration</span></span>

<span data-ttu-id="f0718-694">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="f0718-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="f0718-695">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="f0718-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="f0718-696">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="f0718-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="f0718-697">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="f0718-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="f0718-698">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="f0718-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="f0718-699">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="f0718-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="f0718-700">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="f0718-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="f0718-701">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f0718-701">HTTP/1.1 only.</span></span> <span data-ttu-id="f0718-702">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="f0718-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="f0718-703">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f0718-703">HTTP/2 only.</span></span> <span data-ttu-id="f0718-704">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="f0718-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="f0718-705">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f0718-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="f0718-706">HTTP/2 需要 TLS 和[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f0718-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="f0718-707">默认协议是 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f0718-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="f0718-708">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="f0718-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="f0718-709">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f0718-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="f0718-710">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="f0718-710">Renegotiation disabled</span></span>
* <span data-ttu-id="f0718-711">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="f0718-711">Compression disabled</span></span>
* <span data-ttu-id="f0718-712">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="f0718-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="f0718-713">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 最小 224 位</span><span class="sxs-lookup"><span data-stu-id="f0718-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="f0718-714">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="f0718-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="f0718-715">密码套件未列入阻止列表</span><span class="sxs-lookup"><span data-stu-id="f0718-715">Cipher suite not blacklisted</span></span>

<span data-ttu-id="f0718-716">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="f0718-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="f0718-717">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="f0718-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="f0718-718">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="f0718-718">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="f0718-719">（可选）创建 `IConnectionAdapter` 实现，以针对特定密码的每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="f0718-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="f0718-720">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="f0718-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="f0718-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="f0718-722">在以下 appsettings.json 示例中，为 Kestrel 的所有终结点建立默认的连接协议（HTTP/1.1 和 HTTP/2）：</span><span class="sxs-lookup"><span data-stu-id="f0718-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="f0718-723">以下配置文件示例为特定终结点建立了连接协议：</span><span class="sxs-lookup"><span data-stu-id="f0718-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="f0718-724">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="f0718-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="f0718-725">传输配置</span><span class="sxs-lookup"><span data-stu-id="f0718-725">Transport configuration</span></span>

<span data-ttu-id="f0718-726">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="f0718-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="f0718-727">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="f0718-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="f0718-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="f0718-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="f0718-729">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="f0718-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="f0718-730">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="f0718-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="f0718-731">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="f0718-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="f0718-732">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="f0718-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="f0718-733">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="f0718-733">URL prefixes</span></span>

<span data-ttu-id="f0718-734">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="f0718-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="f0718-735">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="f0718-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="f0718-736">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="f0718-737">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="f0718-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="f0718-738">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="f0718-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="f0718-739">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="f0718-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="f0718-740">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="f0718-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="f0718-741">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="f0718-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="f0718-742">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="f0718-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="f0718-743">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="f0718-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="f0718-744">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="f0718-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="f0718-745">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="f0718-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="f0718-746">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="f0718-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="f0718-747">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="f0718-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="f0718-748">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="f0718-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="f0718-749">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="f0718-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="f0718-750">主机筛选</span><span class="sxs-lookup"><span data-stu-id="f0718-750">Host filtering</span></span>

<span data-ttu-id="f0718-751">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="f0718-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="f0718-752">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="f0718-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="f0718-753">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f0718-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="f0718-754">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="f0718-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="f0718-755">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="f0718-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="f0718-756">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="f0718-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="f0718-757">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="f0718-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="f0718-758">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="f0718-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="f0718-759">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="f0718-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="f0718-760">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="f0718-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="f0718-761">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="f0718-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="f0718-762">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="f0718-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="f0718-763">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="f0718-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="f0718-764">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="f0718-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="f0718-765">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="f0718-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="f0718-766">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="f0718-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="f0718-767">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="f0718-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="f0718-768">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="f0718-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="f0718-769">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="f0718-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="f0718-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="f0718-770">HTTPS</span></span>
* <span data-ttu-id="f0718-771">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="f0718-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="f0718-772">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="f0718-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="f0718-773">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="f0718-774">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f0718-774">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="f0718-775">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="f0718-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="f0718-776">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="f0718-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="f0718-777">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="f0718-778">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="f0718-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="f0718-780">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-780">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="f0718-782">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="f0718-783">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="f0718-784">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="f0718-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="f0718-785">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="f0718-786">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="f0718-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="f0718-787">反向代理：</span><span class="sxs-lookup"><span data-stu-id="f0718-787">A reverse proxy:</span></span>

* <span data-ttu-id="f0718-788">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="f0718-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="f0718-789">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="f0718-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="f0718-790">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="f0718-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="f0718-791">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="f0718-792">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="f0718-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="f0718-793">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="f0718-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="f0718-794">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="f0718-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="f0718-795">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="f0718-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f0718-796">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="f0718-797">在 Program.cs 中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="f0718-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="f0718-798">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="f0718-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="f0718-799">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”部分。</span><span class="sxs-lookup"><span data-stu-id="f0718-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="f0718-800">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="f0718-800">Kestrel options</span></span>

<span data-ttu-id="f0718-801">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="f0718-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="f0718-802">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="f0718-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="f0718-803">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="f0718-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="f0718-804">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="f0718-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="f0718-805">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="f0718-806">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="f0718-807">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f0718-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="f0718-808">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="f0718-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="f0718-809">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="f0718-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="f0718-810">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="f0718-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="f0718-811">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="f0718-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="f0718-812">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="f0718-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="f0718-813">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="f0718-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="f0718-814">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="f0718-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="f0718-815">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="f0718-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="f0718-816">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="f0718-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="f0718-817">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="f0718-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="f0718-818">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="f0718-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="f0718-819">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="f0718-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="f0718-820">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="f0718-821">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="f0718-822">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="f0718-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="f0718-823">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="f0718-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="f0718-824">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="f0718-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="f0718-825">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="f0718-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="f0718-826">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="f0718-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="f0718-827">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="f0718-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="f0718-828">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="f0718-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="f0718-829">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="f0718-830">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="f0718-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="f0718-831">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="f0718-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="f0718-832">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="f0718-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="f0718-833">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="f0718-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="f0718-834">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="f0718-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="f0718-835">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="f0718-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="f0718-836">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="f0718-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="f0718-837">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="f0718-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="f0718-838">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="f0718-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="f0718-839">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="f0718-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="f0718-840">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="f0718-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="f0718-841">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="f0718-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="f0718-842">同步 IO</span><span class="sxs-lookup"><span data-stu-id="f0718-842">Synchronous IO</span></span>

<span data-ttu-id="f0718-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 IO。</span><span class="sxs-lookup"><span data-stu-id="f0718-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="f0718-844">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f0718-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f0718-845">大量的阻止同步 IO 操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="f0718-845">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="f0718-846">仅在使用不支持异步 IO 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="f0718-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="f0718-847">下面的示例禁用同步 IO：</span><span class="sxs-lookup"><span data-stu-id="f0718-847">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="f0718-848">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="f0718-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="f0718-849">终结点配置</span><span class="sxs-lookup"><span data-stu-id="f0718-849">Endpoint configuration</span></span>

<span data-ttu-id="f0718-850">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="f0718-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="f0718-851">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="f0718-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="f0718-852">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="f0718-852">Specify URLs using the:</span></span>

* <span data-ttu-id="f0718-853">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="f0718-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="f0718-854">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="f0718-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="f0718-855">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="f0718-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="f0718-856">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="f0718-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="f0718-857">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="f0718-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="f0718-858">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="f0718-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="f0718-859">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="f0718-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="f0718-860">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="f0718-860">A development certificate is created:</span></span>

* <span data-ttu-id="f0718-861">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="f0718-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="f0718-862">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="f0718-863">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="f0718-864">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="f0718-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f0718-865">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="f0718-866">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="f0718-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="f0718-867">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="f0718-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="f0718-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="f0718-869">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="f0718-870">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f0718-871">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="f0718-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="f0718-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="f0718-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="f0718-873">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="f0718-874">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f0718-875">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="f0718-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="f0718-876">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="f0718-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="f0718-877">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f0718-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="f0718-878">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="f0718-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="f0718-879">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="f0718-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="f0718-880">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="f0718-881">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="f0718-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="f0718-882">`UseHttps` &ndash; 将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-882">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="f0718-883">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="f0718-883">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="f0718-884">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="f0718-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="f0718-885">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="f0718-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="f0718-886">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="f0718-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="f0718-887">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0718-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="f0718-888">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="f0718-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="f0718-889">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="f0718-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="f0718-890">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="f0718-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="f0718-891">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="f0718-892">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="f0718-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="f0718-893">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="f0718-894">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="f0718-895">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="f0718-896">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-896">Supported configurations described next:</span></span>

* <span data-ttu-id="f0718-897">无配置</span><span class="sxs-lookup"><span data-stu-id="f0718-897">No configuration</span></span>
* <span data-ttu-id="f0718-898">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="f0718-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="f0718-899">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="f0718-899">Change the defaults in code</span></span>

<span data-ttu-id="f0718-900">*无配置*</span><span class="sxs-lookup"><span data-stu-id="f0718-900">*No configuration*</span></span>

<span data-ttu-id="f0718-901">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="f0718-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="f0718-902">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="f0718-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="f0718-903">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="f0718-904">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="f0718-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="f0718-905">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="f0718-906">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="f0718-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="f0718-907">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="f0718-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="f0718-908">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="f0718-909">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="f0718-910">例如，可将 Certificates > Default 证书指定为：</span><span class="sxs-lookup"><span data-stu-id="f0718-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="f0718-911">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="f0718-911">Schema notes:</span></span>

* <span data-ttu-id="f0718-912">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="f0718-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="f0718-913">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="f0718-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="f0718-914">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="f0718-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="f0718-915">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="f0718-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="f0718-916">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="f0718-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="f0718-917">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="f0718-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="f0718-918">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="f0718-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="f0718-919">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="f0718-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="f0718-920">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="f0718-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="f0718-921">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="f0718-922">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="f0718-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="f0718-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="f0718-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="f0718-924">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="f0718-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="f0718-925">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="f0718-926">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="f0718-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="f0718-927">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="f0718-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="f0718-928">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="f0718-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="f0718-929">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="f0718-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="f0718-930">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="f0718-931">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="f0718-931">*Change the defaults in code*</span></span>

<span data-ttu-id="f0718-932">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="f0718-933">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="f0718-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="f0718-934">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="f0718-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="f0718-935">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="f0718-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="f0718-936">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="f0718-937">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="f0718-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="f0718-938">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="f0718-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="f0718-939">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="f0718-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="f0718-940">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="f0718-940">SNI support requires:</span></span>

* <span data-ttu-id="f0718-941">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="f0718-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="f0718-942">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="f0718-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="f0718-943">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="f0718-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="f0718-944">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="f0718-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="f0718-945">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="f0718-946">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="f0718-946">Connection logging</span></span>

<span data-ttu-id="f0718-947">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="f0718-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="f0718-948">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="f0718-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="f0718-949">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="f0718-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="f0718-950">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="f0718-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="f0718-951">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="f0718-951">Bind to a TCP socket</span></span>

<span data-ttu-id="f0718-952"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="f0718-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="f0718-953">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="f0718-954">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="f0718-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="f0718-955">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="f0718-955">Bind to a Unix socket</span></span>

<span data-ttu-id="f0718-956">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="f0718-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="f0718-957">在 Nginx confiuguration 文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="f0718-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="f0718-958">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="f0718-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="f0718-959">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="f0718-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="f0718-960">端口 0</span><span class="sxs-lookup"><span data-stu-id="f0718-960">Port 0</span></span>

<span data-ttu-id="f0718-961">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="f0718-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="f0718-962">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="f0718-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="f0718-963">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="f0718-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="f0718-964">限制</span><span class="sxs-lookup"><span data-stu-id="f0718-964">Limitations</span></span>

<span data-ttu-id="f0718-965">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="f0718-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="f0718-966">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="f0718-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="f0718-967">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="f0718-967">`urls` host configuration key</span></span>
* <span data-ttu-id="f0718-968">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="f0718-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="f0718-969">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="f0718-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="f0718-970">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="f0718-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="f0718-971">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="f0718-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="f0718-972">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="f0718-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="f0718-973">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="f0718-973">IIS endpoint configuration</span></span>

<span data-ttu-id="f0718-974">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="f0718-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="f0718-975">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="f0718-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="f0718-976">传输配置</span><span class="sxs-lookup"><span data-stu-id="f0718-976">Transport configuration</span></span>

<span data-ttu-id="f0718-977">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="f0718-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="f0718-978">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="f0718-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="f0718-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="f0718-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="f0718-980">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="f0718-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="f0718-981">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="f0718-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="f0718-982">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="f0718-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="f0718-983">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="f0718-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="f0718-984">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="f0718-984">URL prefixes</span></span>

<span data-ttu-id="f0718-985">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="f0718-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="f0718-986">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="f0718-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="f0718-987">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f0718-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="f0718-988">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="f0718-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="f0718-989">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="f0718-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="f0718-990">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="f0718-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="f0718-991">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="f0718-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="f0718-992">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="f0718-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="f0718-993">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="f0718-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="f0718-994">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="f0718-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="f0718-995">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="f0718-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="f0718-996">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="f0718-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="f0718-997">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="f0718-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="f0718-998">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="f0718-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="f0718-999">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="f0718-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="f0718-1000">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="f0718-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="f0718-1001">主机筛选</span><span class="sxs-lookup"><span data-stu-id="f0718-1001">Host filtering</span></span>

<span data-ttu-id="f0718-1002">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="f0718-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="f0718-1003">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="f0718-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="f0718-1004">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f0718-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="f0718-1005">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="f0718-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="f0718-1006">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="f0718-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="f0718-1007">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="f0718-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="f0718-1008">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="f0718-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="f0718-1009">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="f0718-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="f0718-1010">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="f0718-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="f0718-1011">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="f0718-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="f0718-1012">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="f0718-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="f0718-1013">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="f0718-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="f0718-1014">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="f0718-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="f0718-1015">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="f0718-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="f0718-1016">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="f0718-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="f0718-1017">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="f0718-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f0718-1018">其他资源</span><span class="sxs-lookup"><span data-stu-id="f0718-1018">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="f0718-1019">RFC 7230：消息语法和路由（5.4 节：主机）</span><span class="sxs-lookup"><span data-stu-id="f0718-1019">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
