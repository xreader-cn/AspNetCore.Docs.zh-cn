---
title: ASP.NET Core 中的 Kestrel Web 服务器实现
author: rick-anderson
description: 了解跨平台 ASP.NET Core Web 服务器 Kestrel。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 44558a0f2fdc61eb860223658f5bef1d0117ba87
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653931"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="686a0-103">ASP.NET Core 中的 Kestrel Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="686a0-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="686a0-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher) 和 [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="686a0-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="686a0-105">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="686a0-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="686a0-106">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="686a0-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="686a0-107">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="686a0-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="686a0-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="686a0-108">HTTPS</span></span>
* <span data-ttu-id="686a0-109">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="686a0-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="686a0-110">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="686a0-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="686a0-111">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="686a0-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="686a0-112">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="686a0-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="686a0-113">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="686a0-114">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="686a0-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="686a0-115">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="686a0-115">HTTP/2 support</span></span>

<span data-ttu-id="686a0-116">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="686a0-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="686a0-117">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="686a0-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="686a0-118">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="686a0-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="686a0-119">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="686a0-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="686a0-120">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="686a0-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="686a0-121">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="686a0-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="686a0-122">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="686a0-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="686a0-123">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="686a0-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="686a0-124">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="686a0-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="686a0-125">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="686a0-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="686a0-126">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="686a0-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="686a0-127">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="686a0-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="686a0-128">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="686a0-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="686a0-129">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="686a0-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="686a0-130">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="686a0-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="686a0-131">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="686a0-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="686a0-132">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="686a0-133">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="686a0-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="686a0-135">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="686a0-137">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="686a0-138">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="686a0-139">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="686a0-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="686a0-140">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="686a0-141">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="686a0-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="686a0-142">反向代理：</span><span class="sxs-lookup"><span data-stu-id="686a0-142">A reverse proxy:</span></span>

* <span data-ttu-id="686a0-143">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="686a0-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="686a0-144">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="686a0-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="686a0-145">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="686a0-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="686a0-146">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="686a0-147">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="686a0-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="686a0-148">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="686a0-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="686a0-149">ASP.NET Core 应用中的 Kestrel</span><span class="sxs-lookup"><span data-stu-id="686a0-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="686a0-150">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="686a0-151">在“Program.cs”中，<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="686a0-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="686a0-152">有关生成主机的详细信息，请参阅 <xref:fundamentals/host/generic-host#set-up-a-host> 的“设置主机”和“默认生成器设置”部分 。</span><span class="sxs-lookup"><span data-stu-id="686a0-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="686a0-153">若要在调用 `ConfigureWebHostDefaults` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="686a0-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="686a0-154">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="686a0-154">Kestrel options</span></span>

<span data-ttu-id="686a0-155">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="686a0-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="686a0-156">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="686a0-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="686a0-157">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="686a0-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="686a0-158">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="686a0-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="686a0-159">在本文后面的示例中，Kestrel 选项是采用 C# 代码配置的。</span><span class="sxs-lookup"><span data-stu-id="686a0-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="686a0-160">还可以使用 [配置提供程序](xref:fundamentals/configuration/index)设置 Kestrel 选项。</span><span class="sxs-lookup"><span data-stu-id="686a0-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="686a0-161">例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置 ：</span><span class="sxs-lookup"><span data-stu-id="686a0-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="686a0-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 和 [终结点配置](#endpoint-configuration) 可以通过配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="686a0-163">其余的 Kestrel 配置必须采用 C# 代码进行配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="686a0-164">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="686a0-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="686a0-165">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="686a0-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="686a0-166">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="686a0-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="686a0-167">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="686a0-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="686a0-168">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="686a0-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="686a0-169">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="686a0-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="686a0-170">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="686a0-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="686a0-171">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="686a0-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="686a0-172">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="686a0-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="686a0-173">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="686a0-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="686a0-174">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="686a0-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="686a0-175">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="686a0-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="686a0-176">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="686a0-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="686a0-177">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="686a0-178">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="686a0-179">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="686a0-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="686a0-180">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="686a0-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="686a0-181">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="686a0-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="686a0-182">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="686a0-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="686a0-183">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="686a0-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="686a0-184">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="686a0-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="686a0-185">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="686a0-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="686a0-186">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="686a0-187">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="686a0-188">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="686a0-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="686a0-189">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="686a0-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="686a0-190">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="686a0-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="686a0-191">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="686a0-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="686a0-192">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="686a0-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="686a0-193">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="686a0-194">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="686a0-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="686a0-195">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="686a0-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="686a0-196">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="686a0-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="686a0-197">用于 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>，因为鉴于协议支持请求多路复用，HTTP/2 通常不支持按请求修改速率限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="686a0-198">不过，用于 HTTP/2 请求的 `HttpContext.Features` 中仍存在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>，因为仍可以通过将 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 设置为 `null`（甚至对于 HTTP/2 请求），按请求完全禁用读取速率限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="686a0-199">对于给定 HTTP/2 请求，尝试读取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或尝试将它设置为除 `null` 以外的值会导致 `NotSupportedException` 抛出。</span><span class="sxs-lookup"><span data-stu-id="686a0-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="686a0-200">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="686a0-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="686a0-201">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="686a0-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="686a0-202">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="686a0-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="686a0-203">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="686a0-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="686a0-204">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="686a0-204">Maximum streams per connection</span></span>

<span data-ttu-id="686a0-205">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="686a0-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="686a0-206">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="686a0-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="686a0-207">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="686a0-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="686a0-208">标题表大小</span><span class="sxs-lookup"><span data-stu-id="686a0-208">Header table size</span></span>

<span data-ttu-id="686a0-209">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="686a0-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="686a0-210">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="686a0-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="686a0-211">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="686a0-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="686a0-212">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="686a0-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="686a0-213">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="686a0-213">Maximum frame size</span></span>

<span data-ttu-id="686a0-214">`Http2.MaxFrameSize` 表示服务器接收或发送的 HTTP/2 连接帧有效负载的最大允许大小。</span><span class="sxs-lookup"><span data-stu-id="686a0-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="686a0-215">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="686a0-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="686a0-216">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="686a0-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="686a0-217">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="686a0-217">Maximum request header size</span></span>

<span data-ttu-id="686a0-218">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="686a0-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="686a0-219">此限制适用于名称和值的压缩和未压缩表示形式。</span><span class="sxs-lookup"><span data-stu-id="686a0-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="686a0-220">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="686a0-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="686a0-221">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="686a0-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="686a0-222">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="686a0-222">Initial connection window size</span></span>

<span data-ttu-id="686a0-223">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="686a0-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="686a0-224">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="686a0-225">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="686a0-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="686a0-226">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="686a0-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="686a0-227">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="686a0-227">Initial stream window size</span></span>

<span data-ttu-id="686a0-228">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="686a0-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="686a0-229">请求也受 `Http2.InitialConnectionWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="686a0-230">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="686a0-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="686a0-231">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="686a0-231">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="686a0-232">预告片</span><span class="sxs-lookup"><span data-stu-id="686a0-232">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="686a0-233">重置</span><span class="sxs-lookup"><span data-stu-id="686a0-233">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="686a0-234">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="686a0-234">Synchronous I/O</span></span>

<span data-ttu-id="686a0-235"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="686a0-235"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="686a0-236">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="686a0-236">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="686a0-237">大量阻止同步 I/O 的操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-237">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="686a0-238">仅在使用不支持异步 I/O 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="686a0-238">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="686a0-239">以下示例会启用同步 I/O：</span><span class="sxs-lookup"><span data-stu-id="686a0-239">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="686a0-240">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="686a0-240">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="686a0-241">终结点配置</span><span class="sxs-lookup"><span data-stu-id="686a0-241">Endpoint configuration</span></span>

<span data-ttu-id="686a0-242">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="686a0-242">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="686a0-243">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="686a0-243">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="686a0-244">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="686a0-244">Specify URLs using the:</span></span>

* <span data-ttu-id="686a0-245">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="686a0-245">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="686a0-246">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="686a0-246">`--urls` command-line argument.</span></span>
* <span data-ttu-id="686a0-247">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="686a0-247">`urls` host configuration key.</span></span>
* <span data-ttu-id="686a0-248">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="686a0-248">`UseUrls` extension method.</span></span>

<span data-ttu-id="686a0-249">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="686a0-249">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="686a0-250">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="686a0-250">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="686a0-251">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="686a0-251">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="686a0-252">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="686a0-252">A development certificate is created:</span></span>

* <span data-ttu-id="686a0-253">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="686a0-253">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="686a0-254">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-254">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="686a0-255">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-255">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="686a0-256">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="686a0-256">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="686a0-257">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-257">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="686a0-258">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="686a0-258">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="686a0-259">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-259">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="686a0-260">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="686a0-260">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="686a0-261">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-261">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="686a0-262">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-262">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="686a0-263">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="686a0-263">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="686a0-264">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="686a0-264">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="686a0-265">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-265">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="686a0-266">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-266">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="686a0-267">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="686a0-267">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="686a0-268">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="686a0-268">Configure(IConfiguration)</span></span>

<span data-ttu-id="686a0-269">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-269">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="686a0-270">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="686a0-270">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="686a0-271">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="686a0-271">ListenOptions.UseHttps</span></span>

<span data-ttu-id="686a0-272">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-272">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="686a0-273">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="686a0-273">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="686a0-274">`UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-274">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="686a0-275">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="686a0-275">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="686a0-276">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="686a0-276">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="686a0-277">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="686a0-277">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="686a0-278">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="686a0-278">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="686a0-279">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-279">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="686a0-280">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="686a0-280">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="686a0-281">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="686a0-281">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="686a0-282">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="686a0-282">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="686a0-283">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-283">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="686a0-284">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="686a0-284">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="686a0-285">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-285">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="686a0-286">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-286">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="686a0-287">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-287">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="686a0-288">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-288">Supported configurations described next:</span></span>

* <span data-ttu-id="686a0-289">无配置</span><span class="sxs-lookup"><span data-stu-id="686a0-289">No configuration</span></span>
* <span data-ttu-id="686a0-290">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="686a0-290">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="686a0-291">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="686a0-291">Change the defaults in code</span></span>

<span data-ttu-id="686a0-292">*无配置*</span><span class="sxs-lookup"><span data-stu-id="686a0-292">*No configuration*</span></span>

<span data-ttu-id="686a0-293">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="686a0-293">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="686a0-294">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="686a0-294">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="686a0-295">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-295">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="686a0-296">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="686a0-296">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="686a0-297">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-297">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="686a0-298">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="686a0-298">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="686a0-299">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="686a0-299">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="686a0-300">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书  。</span><span class="sxs-lookup"><span data-stu-id="686a0-300">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="686a0-301">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书 。</span><span class="sxs-lookup"><span data-stu-id="686a0-301">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="686a0-302">例如，可将 Certificates > Default 证书指定为 ：</span><span class="sxs-lookup"><span data-stu-id="686a0-302">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="686a0-303">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="686a0-303">Schema notes:</span></span>

* <span data-ttu-id="686a0-304">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="686a0-304">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="686a0-305">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="686a0-305">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="686a0-306">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="686a0-306">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="686a0-307">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="686a0-307">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="686a0-308">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="686a0-308">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="686a0-309">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="686a0-309">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="686a0-310">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="686a0-310">The `Certificate` section is optional.</span></span> <span data-ttu-id="686a0-311">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="686a0-311">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="686a0-312">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="686a0-312">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="686a0-313">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书   。</span><span class="sxs-lookup"><span data-stu-id="686a0-313">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="686a0-314">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="686a0-314">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="686a0-315">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="686a0-315">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="686a0-316">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="686a0-316">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="686a0-317">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-317">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="686a0-318">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-318">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="686a0-319">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="686a0-319">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="686a0-320">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="686a0-320">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="686a0-321">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="686a0-321">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="686a0-322">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-322">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="686a0-323">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="686a0-323">*Change the defaults in code*</span></span>

<span data-ttu-id="686a0-324">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-324">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="686a0-325">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="686a0-325">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="686a0-326">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="686a0-326">*Kestrel support for SNI*</span></span>

<span data-ttu-id="686a0-327">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="686a0-327">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="686a0-328">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-328">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="686a0-329">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="686a0-329">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="686a0-330">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="686a0-330">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="686a0-331">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-331">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="686a0-332">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="686a0-332">SNI support requires:</span></span>

* <span data-ttu-id="686a0-333">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="686a0-333">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="686a0-334">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="686a0-334">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="686a0-335">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="686a0-335">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="686a0-336">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="686a0-336">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="686a0-337">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-337">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="686a0-338">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="686a0-338">Connection logging</span></span>

<span data-ttu-id="686a0-339">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="686a0-339">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="686a0-340">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="686a0-340">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="686a0-341">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="686a0-341">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="686a0-342">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="686a0-342">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="686a0-343">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="686a0-343">Bind to a TCP socket</span></span>

<span data-ttu-id="686a0-344"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-344">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="686a0-345">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-345">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="686a0-346">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-346">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="686a0-347">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="686a0-347">Bind to a Unix socket</span></span>

<span data-ttu-id="686a0-348">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="686a0-348">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="686a0-349">在 Nginx 配置文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="686a0-349">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="686a0-350">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="686a0-350">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="686a0-351">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="686a0-351">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="686a0-352">端口 0</span><span class="sxs-lookup"><span data-stu-id="686a0-352">Port 0</span></span>

<span data-ttu-id="686a0-353">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-353">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="686a0-354">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="686a0-354">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="686a0-355">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="686a0-355">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="686a0-356">限制</span><span class="sxs-lookup"><span data-stu-id="686a0-356">Limitations</span></span>

<span data-ttu-id="686a0-357">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="686a0-357">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="686a0-358">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="686a0-358">`--urls` command-line argument</span></span>
* <span data-ttu-id="686a0-359">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="686a0-359">`urls` host configuration key</span></span>
* <span data-ttu-id="686a0-360">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="686a0-360">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="686a0-361">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="686a0-361">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="686a0-362">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="686a0-362">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="686a0-363">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="686a0-363">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="686a0-364">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="686a0-364">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="686a0-365">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="686a0-365">IIS endpoint configuration</span></span>

<span data-ttu-id="686a0-366">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="686a0-366">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="686a0-367">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="686a0-367">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="686a0-368">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="686a0-368">ListenOptions.Protocols</span></span>

<span data-ttu-id="686a0-369">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="686a0-369">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="686a0-370">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="686a0-370">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="686a0-371">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="686a0-371">`HttpProtocols` enum value</span></span> | <span data-ttu-id="686a0-372">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="686a0-372">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="686a0-373">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="686a0-373">HTTP/1.1 only.</span></span> <span data-ttu-id="686a0-374">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="686a0-374">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="686a0-375">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="686a0-375">HTTP/2 only.</span></span> <span data-ttu-id="686a0-376">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="686a0-376">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="686a0-377">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="686a0-377">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="686a0-378">HTTP/2 要求客户端在 TLS [应用层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手过程中选择 HTTP/2；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="686a0-378">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="686a0-379">任何终结点的默认 `ListenOptions.Protocols` 值都为 `HttpProtocols.Http1AndHttp2`。</span><span class="sxs-lookup"><span data-stu-id="686a0-379">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="686a0-380">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="686a0-380">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="686a0-381">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="686a0-381">TLS version 1.2 or later</span></span>
* <span data-ttu-id="686a0-382">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="686a0-382">Renegotiation disabled</span></span>
* <span data-ttu-id="686a0-383">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="686a0-383">Compression disabled</span></span>
* <span data-ttu-id="686a0-384">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="686a0-384">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="686a0-385">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;：最小 224 位</span><span class="sxs-lookup"><span data-stu-id="686a0-385">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="686a0-386">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;：最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="686a0-386">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="686a0-387">不禁止密码套件。</span><span class="sxs-lookup"><span data-stu-id="686a0-387">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="686a0-388">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="686a0-388">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="686a0-389">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="686a0-389">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="686a0-390">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="686a0-390">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="686a0-391">使用连接中间件，针对特定密码的每个连接筛选 TLS 握手（如有必要）。</span><span class="sxs-lookup"><span data-stu-id="686a0-391">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="686a0-392">下面的示例针对应用不支持的任何密码算法引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="686a0-392">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="686a0-393">或者，定义 [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 并将其与可接受的密码套件列表进行比较。</span><span class="sxs-lookup"><span data-stu-id="686a0-393">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="686a0-394">没有哪种加密是使用 [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 密码算法。</span><span class="sxs-lookup"><span data-stu-id="686a0-394">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="686a0-395">连接筛选也可以通过 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda 进行配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-395">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="686a0-396">在 Linux 上，<xref:System.Net.Security.CipherSuitesPolicy> 可用于针对每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="686a0-396">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="686a0-397">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="686a0-397">*Set the protocol from configuration*</span></span>

<span data-ttu-id="686a0-398">`CreateDefaultBuilder` 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-398">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="686a0-399">以下 appsettings.json 示例将 HTTP/1.1 建立为所有终结点的默认连接协议：</span><span class="sxs-lookup"><span data-stu-id="686a0-399">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="686a0-400">以下 appsettings.json 示例将 HTTP/1.1 建立为所有指定终结点的连接协议：</span><span class="sxs-lookup"><span data-stu-id="686a0-400">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="686a0-401">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="686a0-401">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="686a0-402">传输配置</span><span class="sxs-lookup"><span data-stu-id="686a0-402">Transport configuration</span></span>

<span data-ttu-id="686a0-403">对于需要使用 Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) 的项目：</span><span class="sxs-lookup"><span data-stu-id="686a0-403">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="686a0-404">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="686a0-404">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="686a0-405">调用 `IWebHostBuilder` 上的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="686a0-405">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="686a0-406">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="686a0-406">URL prefixes</span></span>

<span data-ttu-id="686a0-407">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="686a0-407">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="686a0-408">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="686a0-408">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="686a0-409">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-409">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="686a0-410">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="686a0-410">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="686a0-411">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="686a0-411">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="686a0-412">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="686a0-412">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="686a0-413">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="686a0-413">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="686a0-414">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="686a0-414">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="686a0-415">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="686a0-415">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="686a0-416">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="686a0-416">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="686a0-417">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="686a0-417">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="686a0-418">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="686a0-418">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="686a0-419">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="686a0-419">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="686a0-420">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="686a0-420">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="686a0-421">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="686a0-421">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="686a0-422">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="686a0-422">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="686a0-423">主机筛选</span><span class="sxs-lookup"><span data-stu-id="686a0-423">Host filtering</span></span>

<span data-ttu-id="686a0-424">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="686a0-424">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="686a0-425">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="686a0-425">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="686a0-426">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="686a0-426">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="686a0-427">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="686a0-427">`Host` headers aren't validated.</span></span>

<span data-ttu-id="686a0-428">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="686a0-428">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="686a0-429">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包为 ASP.NET Core 应用隐式提供。</span><span class="sxs-lookup"><span data-stu-id="686a0-429">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="686a0-430">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="686a0-430">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="686a0-431">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="686a0-431">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="686a0-432">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="686a0-432">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="686a0-433">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="686a0-433">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="686a0-434">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="686a0-434">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="686a0-435">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="686a0-435">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="686a0-436">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="686a0-436">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="686a0-437">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="686a0-437">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="686a0-438">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="686a0-438">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="686a0-439">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="686a0-439">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="686a0-440">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="686a0-440">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="686a0-441">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="686a0-441">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="686a0-442">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="686a0-442">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="686a0-443">HTTPS</span><span class="sxs-lookup"><span data-stu-id="686a0-443">HTTPS</span></span>
* <span data-ttu-id="686a0-444">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="686a0-444">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="686a0-445">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="686a0-445">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="686a0-446">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="686a0-446">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="686a0-447">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="686a0-447">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="686a0-448">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-448">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="686a0-449">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="686a0-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="686a0-450">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="686a0-450">HTTP/2 support</span></span>

<span data-ttu-id="686a0-451">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="686a0-451">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="686a0-452">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="686a0-452">Operating system&dagger;</span></span>
  * <span data-ttu-id="686a0-453">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="686a0-453">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="686a0-454">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="686a0-454">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="686a0-455">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="686a0-455">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="686a0-456">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="686a0-456">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="686a0-457">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="686a0-457">TLS 1.2 or later connection</span></span>

<span data-ttu-id="686a0-458">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="686a0-458">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="686a0-459">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="686a0-459">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="686a0-460">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="686a0-460">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="686a0-461">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="686a0-461">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="686a0-462">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="686a0-462">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="686a0-463">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="686a0-463">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="686a0-464">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="686a0-464">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="686a0-465">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="686a0-465">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="686a0-466">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="686a0-466">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="686a0-467">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-467">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="686a0-468">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="686a0-468">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="686a0-470">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-470">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="686a0-472">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-472">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="686a0-473">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-473">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="686a0-474">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="686a0-474">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="686a0-475">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-475">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="686a0-476">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="686a0-476">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="686a0-477">反向代理：</span><span class="sxs-lookup"><span data-stu-id="686a0-477">A reverse proxy:</span></span>

* <span data-ttu-id="686a0-478">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="686a0-478">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="686a0-479">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="686a0-479">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="686a0-480">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="686a0-480">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="686a0-481">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-481">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="686a0-482">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="686a0-482">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="686a0-483">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="686a0-483">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="686a0-484">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="686a0-484">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="686a0-485">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="686a0-485">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="686a0-486">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-486">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="686a0-487">在 Program.cs 中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="686a0-487">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="686a0-488">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”部分。</span><span class="sxs-lookup"><span data-stu-id="686a0-488">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="686a0-489">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="686a0-489">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="686a0-490">如果应用未调用 `CreateDefaultBuilder` 来设置主机，请在调用 `ConfigureKestrel` 之前先调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="686a0-490">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="686a0-491">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="686a0-491">Kestrel options</span></span>

<span data-ttu-id="686a0-492">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="686a0-492">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="686a0-493">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="686a0-493">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="686a0-494">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="686a0-494">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="686a0-495">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="686a0-495">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="686a0-496">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-496">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="686a0-497">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置 ：</span><span class="sxs-lookup"><span data-stu-id="686a0-497">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="686a0-498">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="686a0-498">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="686a0-499">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="686a0-499">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="686a0-500">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="686a0-500">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="686a0-501">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="686a0-501">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="686a0-502">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="686a0-502">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="686a0-503">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="686a0-503">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="686a0-504">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="686a0-504">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="686a0-505">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="686a0-505">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="686a0-506">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="686a0-506">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="686a0-507">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="686a0-507">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="686a0-508">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="686a0-508">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="686a0-509">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="686a0-509">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="686a0-510">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="686a0-510">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="686a0-511">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-511">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="686a0-512">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-512">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="686a0-513">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="686a0-513">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="686a0-514">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="686a0-514">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="686a0-515">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="686a0-515">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="686a0-516">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="686a0-516">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="686a0-517">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="686a0-517">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="686a0-518">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="686a0-518">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="686a0-519">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="686a0-519">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="686a0-520">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-520">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="686a0-521">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-521">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="686a0-522">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="686a0-522">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="686a0-523">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="686a0-523">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="686a0-524">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="686a0-524">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="686a0-525">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="686a0-525">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="686a0-526">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="686a0-526">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="686a0-527">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-527">A minimum rate also applies to the response.</span></span> <span data-ttu-id="686a0-528">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="686a0-528">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="686a0-529">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="686a0-529">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="686a0-530">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="686a0-530">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="686a0-531">由于协议支持请求多路复用，HTTP/2 不支持基于每个请求修改速率限制，因此 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的速率特性。</span><span class="sxs-lookup"><span data-stu-id="686a0-531">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="686a0-532">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="686a0-532">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="686a0-533">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="686a0-533">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="686a0-534">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="686a0-534">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="686a0-535">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="686a0-535">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="686a0-536">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="686a0-536">Maximum streams per connection</span></span>

<span data-ttu-id="686a0-537">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="686a0-537">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="686a0-538">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="686a0-538">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="686a0-539">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="686a0-539">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="686a0-540">标题表大小</span><span class="sxs-lookup"><span data-stu-id="686a0-540">Header table size</span></span>

<span data-ttu-id="686a0-541">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="686a0-541">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="686a0-542">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="686a0-542">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="686a0-543">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="686a0-543">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="686a0-544">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="686a0-544">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="686a0-545">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="686a0-545">Maximum frame size</span></span>

<span data-ttu-id="686a0-546">`Http2.MaxFrameSize` 指示要接收的 HTTP/2 连接帧有效负载的最大大小。</span><span class="sxs-lookup"><span data-stu-id="686a0-546">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="686a0-547">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="686a0-547">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="686a0-548">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="686a0-548">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="686a0-549">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="686a0-549">Maximum request header size</span></span>

<span data-ttu-id="686a0-550">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="686a0-550">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="686a0-551">此限制同时适用于压缩和未压缩表示形式中的名称和值。</span><span class="sxs-lookup"><span data-stu-id="686a0-551">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="686a0-552">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="686a0-552">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="686a0-553">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="686a0-553">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="686a0-554">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="686a0-554">Initial connection window size</span></span>

<span data-ttu-id="686a0-555">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="686a0-555">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="686a0-556">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-556">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="686a0-557">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="686a0-557">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="686a0-558">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="686a0-558">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="686a0-559">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="686a0-559">Initial stream window size</span></span>

<span data-ttu-id="686a0-560">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="686a0-560">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="686a0-561">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-561">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="686a0-562">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="686a0-562">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="686a0-563">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="686a0-563">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="686a0-564">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="686a0-564">Synchronous I/O</span></span>

<span data-ttu-id="686a0-565"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="686a0-565"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="686a0-566">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="686a0-566">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="686a0-567">大量阻止同步 I/O 的操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-567">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="686a0-568">仅在使用不支持异步 I/O 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="686a0-568">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="686a0-569">以下示例会启用同步 I/O：</span><span class="sxs-lookup"><span data-stu-id="686a0-569">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="686a0-570">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="686a0-570">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="686a0-571">终结点配置</span><span class="sxs-lookup"><span data-stu-id="686a0-571">Endpoint configuration</span></span>

<span data-ttu-id="686a0-572">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="686a0-572">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="686a0-573">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="686a0-573">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="686a0-574">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="686a0-574">Specify URLs using the:</span></span>

* <span data-ttu-id="686a0-575">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="686a0-575">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="686a0-576">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="686a0-576">`--urls` command-line argument.</span></span>
* <span data-ttu-id="686a0-577">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="686a0-577">`urls` host configuration key.</span></span>
* <span data-ttu-id="686a0-578">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="686a0-578">`UseUrls` extension method.</span></span>

<span data-ttu-id="686a0-579">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="686a0-579">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="686a0-580">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="686a0-580">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="686a0-581">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="686a0-581">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="686a0-582">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="686a0-582">A development certificate is created:</span></span>

* <span data-ttu-id="686a0-583">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="686a0-583">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="686a0-584">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-584">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="686a0-585">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-585">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="686a0-586">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="686a0-586">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="686a0-587">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-587">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="686a0-588">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="686a0-588">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="686a0-589">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-589">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="686a0-590">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="686a0-590">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="686a0-591">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-591">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="686a0-592">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-592">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="686a0-593">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="686a0-593">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="686a0-594">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="686a0-594">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="686a0-595">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-595">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="686a0-596">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-596">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="686a0-597">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="686a0-597">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="686a0-598">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="686a0-598">Configure(IConfiguration)</span></span>

<span data-ttu-id="686a0-599">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-599">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="686a0-600">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="686a0-600">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="686a0-601">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="686a0-601">ListenOptions.UseHttps</span></span>

<span data-ttu-id="686a0-602">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-602">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="686a0-603">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="686a0-603">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="686a0-604">`UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-604">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="686a0-605">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="686a0-605">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="686a0-606">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="686a0-606">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="686a0-607">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="686a0-607">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="686a0-608">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="686a0-608">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="686a0-609">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-609">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="686a0-610">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="686a0-610">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="686a0-611">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="686a0-611">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="686a0-612">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="686a0-612">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="686a0-613">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-613">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="686a0-614">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="686a0-614">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="686a0-615">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-615">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="686a0-616">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-616">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="686a0-617">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-617">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="686a0-618">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-618">Supported configurations described next:</span></span>

* <span data-ttu-id="686a0-619">无配置</span><span class="sxs-lookup"><span data-stu-id="686a0-619">No configuration</span></span>
* <span data-ttu-id="686a0-620">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="686a0-620">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="686a0-621">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="686a0-621">Change the defaults in code</span></span>

<span data-ttu-id="686a0-622">*无配置*</span><span class="sxs-lookup"><span data-stu-id="686a0-622">*No configuration*</span></span>

<span data-ttu-id="686a0-623">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="686a0-623">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="686a0-624">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="686a0-624">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="686a0-625">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-625">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="686a0-626">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="686a0-626">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="686a0-627">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-627">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="686a0-628">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="686a0-628">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="686a0-629">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="686a0-629">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="686a0-630">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书  。</span><span class="sxs-lookup"><span data-stu-id="686a0-630">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="686a0-631">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书 。</span><span class="sxs-lookup"><span data-stu-id="686a0-631">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="686a0-632">例如，可将 Certificates > Default 证书指定为 ：</span><span class="sxs-lookup"><span data-stu-id="686a0-632">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="686a0-633">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="686a0-633">Schema notes:</span></span>

* <span data-ttu-id="686a0-634">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="686a0-634">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="686a0-635">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="686a0-635">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="686a0-636">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="686a0-636">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="686a0-637">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="686a0-637">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="686a0-638">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="686a0-638">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="686a0-639">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="686a0-639">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="686a0-640">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="686a0-640">The `Certificate` section is optional.</span></span> <span data-ttu-id="686a0-641">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="686a0-641">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="686a0-642">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="686a0-642">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="686a0-643">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书   。</span><span class="sxs-lookup"><span data-stu-id="686a0-643">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="686a0-644">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="686a0-644">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="686a0-645">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="686a0-645">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="686a0-646">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="686a0-646">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="686a0-647">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-647">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="686a0-648">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-648">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="686a0-649">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="686a0-649">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="686a0-650">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="686a0-650">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="686a0-651">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="686a0-651">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="686a0-652">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-652">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="686a0-653">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="686a0-653">*Change the defaults in code*</span></span>

<span data-ttu-id="686a0-654">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-654">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="686a0-655">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="686a0-655">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="686a0-656">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="686a0-656">*Kestrel support for SNI*</span></span>

<span data-ttu-id="686a0-657">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="686a0-657">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="686a0-658">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-658">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="686a0-659">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="686a0-659">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="686a0-660">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="686a0-660">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="686a0-661">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-661">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="686a0-662">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="686a0-662">SNI support requires:</span></span>

* <span data-ttu-id="686a0-663">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="686a0-663">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="686a0-664">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="686a0-664">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="686a0-665">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="686a0-665">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="686a0-666">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="686a0-666">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="686a0-667">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-667">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="686a0-668">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="686a0-668">Connection logging</span></span>

<span data-ttu-id="686a0-669">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="686a0-669">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="686a0-670">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="686a0-670">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="686a0-671">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="686a0-671">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="686a0-672">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="686a0-672">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="686a0-673">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="686a0-673">Bind to a TCP socket</span></span>

<span data-ttu-id="686a0-674"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-674">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="686a0-675">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-675">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="686a0-676">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-676">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="686a0-677">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="686a0-677">Bind to a Unix socket</span></span>

<span data-ttu-id="686a0-678">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="686a0-678">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="686a0-679">在 Nginx confiuguration 文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="686a0-679">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="686a0-680">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="686a0-680">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="686a0-681">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="686a0-681">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="686a0-682">端口 0</span><span class="sxs-lookup"><span data-stu-id="686a0-682">Port 0</span></span>

<span data-ttu-id="686a0-683">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-683">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="686a0-684">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="686a0-684">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="686a0-685">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="686a0-685">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="686a0-686">限制</span><span class="sxs-lookup"><span data-stu-id="686a0-686">Limitations</span></span>

<span data-ttu-id="686a0-687">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="686a0-687">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="686a0-688">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="686a0-688">`--urls` command-line argument</span></span>
* <span data-ttu-id="686a0-689">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="686a0-689">`urls` host configuration key</span></span>
* <span data-ttu-id="686a0-690">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="686a0-690">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="686a0-691">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="686a0-691">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="686a0-692">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="686a0-692">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="686a0-693">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="686a0-693">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="686a0-694">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="686a0-694">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="686a0-695">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="686a0-695">IIS endpoint configuration</span></span>

<span data-ttu-id="686a0-696">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="686a0-696">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="686a0-697">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="686a0-697">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="686a0-698">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="686a0-698">ListenOptions.Protocols</span></span>

<span data-ttu-id="686a0-699">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="686a0-699">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="686a0-700">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="686a0-700">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="686a0-701">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="686a0-701">`HttpProtocols` enum value</span></span> | <span data-ttu-id="686a0-702">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="686a0-702">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="686a0-703">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="686a0-703">HTTP/1.1 only.</span></span> <span data-ttu-id="686a0-704">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="686a0-704">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="686a0-705">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="686a0-705">HTTP/2 only.</span></span> <span data-ttu-id="686a0-706">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="686a0-706">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="686a0-707">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="686a0-707">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="686a0-708">HTTP/2 需要 TLS 和[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="686a0-708">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="686a0-709">默认协议是 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="686a0-709">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="686a0-710">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="686a0-710">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="686a0-711">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="686a0-711">TLS version 1.2 or later</span></span>
* <span data-ttu-id="686a0-712">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="686a0-712">Renegotiation disabled</span></span>
* <span data-ttu-id="686a0-713">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="686a0-713">Compression disabled</span></span>
* <span data-ttu-id="686a0-714">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="686a0-714">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="686a0-715">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;：最小 224 位</span><span class="sxs-lookup"><span data-stu-id="686a0-715">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="686a0-716">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;：最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="686a0-716">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="686a0-717">未阻止密码套件</span><span class="sxs-lookup"><span data-stu-id="686a0-717">Cipher suite not blocked</span></span>

<span data-ttu-id="686a0-718">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="686a0-718">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="686a0-719">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="686a0-719">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="686a0-720">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="686a0-720">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="686a0-721">（可选）创建 `IConnectionAdapter` 实现，以针对特定密码的每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="686a0-721">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="686a0-722">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="686a0-722">*Set the protocol from configuration*</span></span>

<span data-ttu-id="686a0-723"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-723"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="686a0-724">在以下 appsettings.json 示例中，为 Kestrel 的所有终结点建立默认的连接协议（HTTP/1.1 和 HTTP/2）：</span><span class="sxs-lookup"><span data-stu-id="686a0-724">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="686a0-725">以下配置文件示例为特定终结点建立了连接协议：</span><span class="sxs-lookup"><span data-stu-id="686a0-725">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="686a0-726">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="686a0-726">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="686a0-727">传输配置</span><span class="sxs-lookup"><span data-stu-id="686a0-727">Transport configuration</span></span>

<span data-ttu-id="686a0-728">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="686a0-728">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="686a0-729">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="686a0-729">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="686a0-730">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="686a0-730">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="686a0-731">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="686a0-731">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="686a0-732">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="686a0-732">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="686a0-733">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="686a0-733">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="686a0-734">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="686a0-734">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="686a0-735">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="686a0-735">URL prefixes</span></span>

<span data-ttu-id="686a0-736">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="686a0-736">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="686a0-737">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="686a0-737">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="686a0-738">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-738">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="686a0-739">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="686a0-739">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="686a0-740">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="686a0-740">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="686a0-741">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="686a0-741">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="686a0-742">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="686a0-742">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="686a0-743">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="686a0-743">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="686a0-744">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="686a0-744">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="686a0-745">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="686a0-745">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="686a0-746">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="686a0-746">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="686a0-747">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="686a0-747">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="686a0-748">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="686a0-748">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="686a0-749">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="686a0-749">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="686a0-750">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="686a0-750">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="686a0-751">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="686a0-751">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="686a0-752">主机筛选</span><span class="sxs-lookup"><span data-stu-id="686a0-752">Host filtering</span></span>

<span data-ttu-id="686a0-753">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="686a0-753">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="686a0-754">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="686a0-754">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="686a0-755">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="686a0-755">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="686a0-756">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="686a0-756">`Host` headers aren't validated.</span></span>

<span data-ttu-id="686a0-757">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="686a0-757">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="686a0-758">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="686a0-758">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="686a0-759">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="686a0-759">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="686a0-760">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="686a0-760">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="686a0-761">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="686a0-761">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="686a0-762">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="686a0-762">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="686a0-763">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="686a0-763">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="686a0-764">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="686a0-764">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="686a0-765">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="686a0-765">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="686a0-766">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="686a0-766">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="686a0-767">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="686a0-767">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="686a0-768">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="686a0-768">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="686a0-769">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="686a0-769">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="686a0-770">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="686a0-770">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="686a0-771">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="686a0-771">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="686a0-772">HTTPS</span><span class="sxs-lookup"><span data-stu-id="686a0-772">HTTPS</span></span>
* <span data-ttu-id="686a0-773">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="686a0-773">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="686a0-774">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="686a0-774">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="686a0-775">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-775">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="686a0-776">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="686a0-776">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="686a0-777">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="686a0-777">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="686a0-778">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="686a0-778">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="686a0-779">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-779">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="686a0-780">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="686a0-780">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="686a0-782">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-782">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="686a0-784">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-784">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="686a0-785">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-785">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="686a0-786">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="686a0-786">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="686a0-787">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-787">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="686a0-788">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="686a0-788">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="686a0-789">反向代理：</span><span class="sxs-lookup"><span data-stu-id="686a0-789">A reverse proxy:</span></span>

* <span data-ttu-id="686a0-790">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="686a0-790">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="686a0-791">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="686a0-791">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="686a0-792">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="686a0-792">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="686a0-793">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-793">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="686a0-794">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="686a0-794">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="686a0-795">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="686a0-795">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="686a0-796">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="686a0-796">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="686a0-797">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="686a0-797">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="686a0-798">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-798">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="686a0-799">在 Program.cs 中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="686a0-799">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="686a0-800">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="686a0-800">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="686a0-801">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”部分。</span><span class="sxs-lookup"><span data-stu-id="686a0-801">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="686a0-802">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="686a0-802">Kestrel options</span></span>

<span data-ttu-id="686a0-803">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="686a0-803">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="686a0-804">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="686a0-804">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="686a0-805">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="686a0-805">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="686a0-806">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="686a0-806">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="686a0-807">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-807">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="686a0-808">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置 ：</span><span class="sxs-lookup"><span data-stu-id="686a0-808">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="686a0-809">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="686a0-809">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="686a0-810">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="686a0-810">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="686a0-811">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="686a0-811">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="686a0-812">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="686a0-812">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="686a0-813">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="686a0-813">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="686a0-814">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="686a0-814">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="686a0-815">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="686a0-815">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="686a0-816">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="686a0-816">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="686a0-817">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="686a0-817">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="686a0-818">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="686a0-818">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="686a0-819">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="686a0-819">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="686a0-820">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="686a0-820">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="686a0-821">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="686a0-821">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="686a0-822">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-822">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="686a0-823">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-823">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="686a0-824">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="686a0-824">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="686a0-825">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="686a0-825">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="686a0-826">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="686a0-826">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="686a0-827">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="686a0-827">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="686a0-828">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="686a0-828">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="686a0-829">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="686a0-829">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="686a0-830">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="686a0-830">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="686a0-831">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-831">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="686a0-832">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="686a0-832">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="686a0-833">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="686a0-833">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="686a0-834">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="686a0-834">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="686a0-835">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="686a0-835">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="686a0-836">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="686a0-836">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="686a0-837">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="686a0-837">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="686a0-838">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-838">A minimum rate also applies to the response.</span></span> <span data-ttu-id="686a0-839">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="686a0-839">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="686a0-840">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="686a0-840">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="686a0-841">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="686a0-841">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="686a0-842">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="686a0-842">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="686a0-843">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="686a0-843">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="686a0-844">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="686a0-844">Synchronous I/O</span></span>

<span data-ttu-id="686a0-845"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="686a0-845"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="686a0-846">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="686a0-846">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="686a0-847">大量阻止同步 I/O 的操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-847">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="686a0-848">仅在使用不支持异步 I/O 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="686a0-848">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="686a0-849">以下示例会禁用同步 I/O：</span><span class="sxs-lookup"><span data-stu-id="686a0-849">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="686a0-850">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="686a0-850">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="686a0-851">终结点配置</span><span class="sxs-lookup"><span data-stu-id="686a0-851">Endpoint configuration</span></span>

<span data-ttu-id="686a0-852">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="686a0-852">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="686a0-853">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="686a0-853">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="686a0-854">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="686a0-854">Specify URLs using the:</span></span>

* <span data-ttu-id="686a0-855">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="686a0-855">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="686a0-856">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="686a0-856">`--urls` command-line argument.</span></span>
* <span data-ttu-id="686a0-857">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="686a0-857">`urls` host configuration key.</span></span>
* <span data-ttu-id="686a0-858">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="686a0-858">`UseUrls` extension method.</span></span>

<span data-ttu-id="686a0-859">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="686a0-859">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="686a0-860">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="686a0-860">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="686a0-861">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="686a0-861">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="686a0-862">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="686a0-862">A development certificate is created:</span></span>

* <span data-ttu-id="686a0-863">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="686a0-863">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="686a0-864">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-864">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="686a0-865">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-865">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="686a0-866">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="686a0-866">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="686a0-867">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-867">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="686a0-868">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="686a0-868">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="686a0-869">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-869">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="686a0-870">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="686a0-870">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="686a0-871">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-871">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="686a0-872">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-872">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="686a0-873">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="686a0-873">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="686a0-874">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="686a0-874">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="686a0-875">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-875">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="686a0-876">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-876">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="686a0-877">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="686a0-877">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="686a0-878">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="686a0-878">Configure(IConfiguration)</span></span>

<span data-ttu-id="686a0-879">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="686a0-879">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="686a0-880">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="686a0-880">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="686a0-881">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="686a0-881">ListenOptions.UseHttps</span></span>

<span data-ttu-id="686a0-882">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-882">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="686a0-883">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="686a0-883">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="686a0-884">`UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-884">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="686a0-885">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="686a0-885">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="686a0-886">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="686a0-886">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="686a0-887">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="686a0-887">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="686a0-888">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="686a0-888">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="686a0-889">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="686a0-889">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="686a0-890">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="686a0-890">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="686a0-891">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="686a0-891">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="686a0-892">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="686a0-892">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="686a0-893">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-893">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="686a0-894">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="686a0-894">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="686a0-895">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-895">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="686a0-896">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-896">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="686a0-897">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-897">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="686a0-898">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-898">Supported configurations described next:</span></span>

* <span data-ttu-id="686a0-899">无配置</span><span class="sxs-lookup"><span data-stu-id="686a0-899">No configuration</span></span>
* <span data-ttu-id="686a0-900">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="686a0-900">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="686a0-901">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="686a0-901">Change the defaults in code</span></span>

<span data-ttu-id="686a0-902">*无配置*</span><span class="sxs-lookup"><span data-stu-id="686a0-902">*No configuration*</span></span>

<span data-ttu-id="686a0-903">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="686a0-903">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="686a0-904">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="686a0-904">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="686a0-905">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-905">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="686a0-906">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="686a0-906">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="686a0-907">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-907">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="686a0-908">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="686a0-908">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="686a0-909">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="686a0-909">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="686a0-910">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书  。</span><span class="sxs-lookup"><span data-stu-id="686a0-910">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="686a0-911">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书 。</span><span class="sxs-lookup"><span data-stu-id="686a0-911">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="686a0-912">例如，可将 Certificates > Default 证书指定为 ：</span><span class="sxs-lookup"><span data-stu-id="686a0-912">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="686a0-913">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="686a0-913">Schema notes:</span></span>

* <span data-ttu-id="686a0-914">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="686a0-914">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="686a0-915">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="686a0-915">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="686a0-916">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="686a0-916">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="686a0-917">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="686a0-917">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="686a0-918">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="686a0-918">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="686a0-919">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="686a0-919">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="686a0-920">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="686a0-920">The `Certificate` section is optional.</span></span> <span data-ttu-id="686a0-921">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="686a0-921">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="686a0-922">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="686a0-922">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="686a0-923">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书   。</span><span class="sxs-lookup"><span data-stu-id="686a0-923">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="686a0-924">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="686a0-924">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="686a0-925">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="686a0-925">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="686a0-926">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="686a0-926">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="686a0-927">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-927">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="686a0-928">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-928">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="686a0-929">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="686a0-929">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="686a0-930">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="686a0-930">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="686a0-931">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="686a0-931">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="686a0-932">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-932">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="686a0-933">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="686a0-933">*Change the defaults in code*</span></span>

<span data-ttu-id="686a0-934">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-934">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="686a0-935">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="686a0-935">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="686a0-936">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="686a0-936">*Kestrel support for SNI*</span></span>

<span data-ttu-id="686a0-937">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="686a0-937">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="686a0-938">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-938">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="686a0-939">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="686a0-939">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="686a0-940">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="686a0-940">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="686a0-941">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="686a0-941">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="686a0-942">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="686a0-942">SNI support requires:</span></span>

* <span data-ttu-id="686a0-943">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="686a0-943">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="686a0-944">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="686a0-944">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="686a0-945">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="686a0-945">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="686a0-946">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="686a0-946">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="686a0-947">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-947">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="686a0-948">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="686a0-948">Connection logging</span></span>

<span data-ttu-id="686a0-949">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="686a0-949">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="686a0-950">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="686a0-950">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="686a0-951">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="686a0-951">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="686a0-952">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="686a0-952">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="686a0-953">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="686a0-953">Bind to a TCP socket</span></span>

<span data-ttu-id="686a0-954"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="686a0-954">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="686a0-955">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-955">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="686a0-956">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="686a0-956">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="686a0-957">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="686a0-957">Bind to a Unix socket</span></span>

<span data-ttu-id="686a0-958">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="686a0-958">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="686a0-959">在 Nginx confiuguration 文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="686a0-959">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="686a0-960">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="686a0-960">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="686a0-961">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="686a0-961">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="686a0-962">端口 0</span><span class="sxs-lookup"><span data-stu-id="686a0-962">Port 0</span></span>

<span data-ttu-id="686a0-963">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="686a0-963">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="686a0-964">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="686a0-964">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="686a0-965">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="686a0-965">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="686a0-966">限制</span><span class="sxs-lookup"><span data-stu-id="686a0-966">Limitations</span></span>

<span data-ttu-id="686a0-967">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="686a0-967">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="686a0-968">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="686a0-968">`--urls` command-line argument</span></span>
* <span data-ttu-id="686a0-969">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="686a0-969">`urls` host configuration key</span></span>
* <span data-ttu-id="686a0-970">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="686a0-970">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="686a0-971">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="686a0-971">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="686a0-972">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="686a0-972">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="686a0-973">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="686a0-973">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="686a0-974">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="686a0-974">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="686a0-975">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="686a0-975">IIS endpoint configuration</span></span>

<span data-ttu-id="686a0-976">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="686a0-976">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="686a0-977">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="686a0-977">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="686a0-978">传输配置</span><span class="sxs-lookup"><span data-stu-id="686a0-978">Transport configuration</span></span>

<span data-ttu-id="686a0-979">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="686a0-979">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="686a0-980">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="686a0-980">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="686a0-981">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="686a0-981">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="686a0-982">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="686a0-982">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="686a0-983">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="686a0-983">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="686a0-984">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="686a0-984">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="686a0-985">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="686a0-985">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="686a0-986">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="686a0-986">URL prefixes</span></span>

<span data-ttu-id="686a0-987">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="686a0-987">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="686a0-988">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="686a0-988">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="686a0-989">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="686a0-989">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="686a0-990">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="686a0-990">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="686a0-991">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="686a0-991">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="686a0-992">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="686a0-992">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="686a0-993">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="686a0-993">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="686a0-994">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="686a0-994">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="686a0-995">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="686a0-995">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="686a0-996">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="686a0-996">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="686a0-997">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="686a0-997">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="686a0-998">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="686a0-998">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="686a0-999">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="686a0-999">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="686a0-1000">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="686a0-1000">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="686a0-1001">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="686a0-1001">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="686a0-1002">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="686a0-1002">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="686a0-1003">主机筛选</span><span class="sxs-lookup"><span data-stu-id="686a0-1003">Host filtering</span></span>

<span data-ttu-id="686a0-1004">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="686a0-1004">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="686a0-1005">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="686a0-1005">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="686a0-1006">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="686a0-1006">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="686a0-1007">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="686a0-1007">`Host` headers aren't validated.</span></span>

<span data-ttu-id="686a0-1008">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="686a0-1008">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="686a0-1009">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="686a0-1009">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="686a0-1010">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="686a0-1010">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="686a0-1011">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="686a0-1011">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="686a0-1012">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="686a0-1012">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="686a0-1013">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="686a0-1013">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="686a0-1014">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="686a0-1014">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="686a0-1015">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="686a0-1015">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="686a0-1016">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="686a0-1016">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="686a0-1017">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="686a0-1017">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="686a0-1018">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="686a0-1018">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="686a0-1019">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="686a0-1019">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="686a0-1020">HTTP/1.1 请求排出</span><span class="sxs-lookup"><span data-stu-id="686a0-1020">HTTP/1.1 request draining</span></span>

<span data-ttu-id="686a0-1021">打开 HTTP 连接非常耗时。</span><span class="sxs-lookup"><span data-stu-id="686a0-1021">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="686a0-1022">对于 HTTPS 而言，这也是资源密集型。</span><span class="sxs-lookup"><span data-stu-id="686a0-1022">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="686a0-1023">因此，Kestrel 会尝试按 HTTP/1.1 协议重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="686a0-1023">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="686a0-1024">请求正文必须完全使用才能允许重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="686a0-1024">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="686a0-1025">应用不会始终使用请求正文，例如 `POST` 请求，其中服务器返回重定向或 404 响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-1025">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="686a0-1026">在 `POST` 重定向的情况下：</span><span class="sxs-lookup"><span data-stu-id="686a0-1026">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="686a0-1027">客户端可能已发送部分 `POST` 数据。</span><span class="sxs-lookup"><span data-stu-id="686a0-1027">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="686a0-1028">服务器写入 301 响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-1028">The server writes the 301 response.</span></span>
* <span data-ttu-id="686a0-1029">在完全读取上一个请求正文中的 `POST` 数据之前，不能将连接用于新请求。</span><span class="sxs-lookup"><span data-stu-id="686a0-1029">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="686a0-1030">Kestrel 尝试排出请求正文。</span><span class="sxs-lookup"><span data-stu-id="686a0-1030">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="686a0-1031">排出请求正文意味着读取和丢弃数据，而不处理数据。</span><span class="sxs-lookup"><span data-stu-id="686a0-1031">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="686a0-1032">排出过程会在允许重新使用连接与排出任何剩余数据所用的时间之间进行权衡：</span><span class="sxs-lookup"><span data-stu-id="686a0-1032">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="686a0-1033">排出超时为五秒，该值不可配置。</span><span class="sxs-lookup"><span data-stu-id="686a0-1033">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="686a0-1034">如果 `Content-Length` 或 `Transfer-Encoding` 标头指定的所有数据在超时之前都未被读取，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="686a0-1034">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="686a0-1035">有时，你可能想要在写入响应之前或之后立即终止请求。</span><span class="sxs-lookup"><span data-stu-id="686a0-1035">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="686a0-1036">例如，客户端可能具有限制性的数据上限，因此可以优先考虑限制上传的数据。</span><span class="sxs-lookup"><span data-stu-id="686a0-1036">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="686a0-1037">在这种情况下，若要终止请求，请从控制器、Razor 页面或中间件调用 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)。</span><span class="sxs-lookup"><span data-stu-id="686a0-1037">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="686a0-1038">在调用 `Abort` 时，存在一些注意事项：</span><span class="sxs-lookup"><span data-stu-id="686a0-1038">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="686a0-1039">创建新连接可能会很慢且成本高昂。</span><span class="sxs-lookup"><span data-stu-id="686a0-1039">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="686a0-1040">在连接关闭之前无法保证客户端已读取响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-1040">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="686a0-1041">应极少调用 `Abort`，并且应将此操作留给严重错误情况（而不是常见错误情况）。</span><span class="sxs-lookup"><span data-stu-id="686a0-1041">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="686a0-1042">只有在需要解决特定问题时才调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="686a0-1042">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="686a0-1043">例如，如果恶意客户端正在尝试 `POST` 数据或在客户端代码中存在导致大量请求的 bug，则调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="686a0-1043">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="686a0-1044">不要为常见错误情况（如 HTTP 404（找不到））调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="686a0-1044">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="686a0-1045">调用 `Abort` 之前调用 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) 可确保服务器已完成写入响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-1045">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="686a0-1046">但是，客户端行为是不可预测的，在连接中止前它们可能未读取响应。</span><span class="sxs-lookup"><span data-stu-id="686a0-1046">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="686a0-1047">对于 HTTP/2，此过程又有所不同，因为协议支持在不关闭连接的情况下中止单个请求流。</span><span class="sxs-lookup"><span data-stu-id="686a0-1047">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="686a0-1048">五秒排出超时不适用。</span><span class="sxs-lookup"><span data-stu-id="686a0-1048">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="686a0-1049">如果在完成响应后存在任何未读的请求正文数据，则服务器会发送 HTTP/2 RST 帧。</span><span class="sxs-lookup"><span data-stu-id="686a0-1049">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="686a0-1050">其他请求正文数据帧将被忽略。</span><span class="sxs-lookup"><span data-stu-id="686a0-1050">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="686a0-1051">如果可能，客户端最好使用 [Expect:100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 请求标头，然后等到服务器响应后再开始发送请求正文。</span><span class="sxs-lookup"><span data-stu-id="686a0-1051">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="686a0-1052">这样，客户端便有机会在发送不需要的数据之前检查响应和中止。</span><span class="sxs-lookup"><span data-stu-id="686a0-1052">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="686a0-1053">其他资源</span><span class="sxs-lookup"><span data-stu-id="686a0-1053">Additional resources</span></span>

* <span data-ttu-id="686a0-1054">在 Linux 上使用 UNIX 套接字时，该套接字不会在应用关闭时自动删除。</span><span class="sxs-lookup"><span data-stu-id="686a0-1054">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="686a0-1055">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="686a0-1055">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="686a0-1056">RFC 7230：消息语法和路由（5.4 节：主机）</span><span class="sxs-lookup"><span data-stu-id="686a0-1056">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
