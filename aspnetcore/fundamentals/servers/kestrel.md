---
title: ASP.NET Core 中的 Kestrel Web 服务器实现
author: guardrex
description: 了解跨平台 ASP.NET Core Web 服务器 Kestrel。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 5565011f6531ef5e95eb02f310e7107f9ed547b2
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378868"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="3d0df-103">ASP.NET Core 中的 Kestrel Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="3d0df-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="3d0df-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)[Stephen Halter](https://twitter.com/halter73) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3d0df-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3d0df-105">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="3d0df-106">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="3d0df-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="3d0df-107">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="3d0df-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="3d0df-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3d0df-108">HTTPS</span></span>
* <span data-ttu-id="3d0df-109">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="3d0df-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="3d0df-110">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3d0df-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="3d0df-111">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="3d0df-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="3d0df-112">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3d0df-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="3d0df-113">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="3d0df-114">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3d0df-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="3d0df-115">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="3d0df-115">HTTP/2 support</span></span>

<span data-ttu-id="3d0df-116">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="3d0df-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="3d0df-117">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="3d0df-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="3d0df-118">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="3d0df-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="3d0df-119">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="3d0df-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="3d0df-120">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3d0df-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="3d0df-121">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="3d0df-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="3d0df-122">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="3d0df-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="3d0df-123">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3d0df-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="3d0df-124">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="3d0df-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="3d0df-125">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="3d0df-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="3d0df-126">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="3d0df-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="3d0df-127">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="3d0df-128">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3d0df-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="3d0df-129">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="3d0df-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="3d0df-130">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="3d0df-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="3d0df-131">可以单独使用 Kestrel，也可以将其与反向代理服务器  （如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="3d0df-132">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="3d0df-133">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="3d0df-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="3d0df-135">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="3d0df-137">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="3d0df-138">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="3d0df-139">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="3d0df-140">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="3d0df-141">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="3d0df-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="3d0df-142">反向代理：</span><span class="sxs-lookup"><span data-stu-id="3d0df-142">A reverse proxy:</span></span>

* <span data-ttu-id="3d0df-143">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="3d0df-144">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="3d0df-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="3d0df-145">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="3d0df-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="3d0df-146">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="3d0df-147">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="3d0df-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="3d0df-148">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="3d0df-149">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="3d0df-149">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="3d0df-150">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="3d0df-151">在 Program.cs  中，模板代码调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="3d0df-151">In *Program.cs*, the template code calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="3d0df-152">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/generic-host#set-up-a-host> 的“设置主机”和“默认生成器设置”   部分。</span><span class="sxs-lookup"><span data-stu-id="3d0df-152">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="3d0df-153">若要在调用 `CreateDefaultBuilder` 和 `ConfigureWebHostDefaults` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="3d0df-153">To provide additional configuration after calling `CreateDefaultBuilder` and `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

<span data-ttu-id="3d0df-154">如果应用未调用 `CreateDefaultBuilder` 来设置主机，请在调用 `ConfigureKestrel` 之前  先调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="3d0df-154">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new HostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseIISIntegration()
            .UseStartup<Startup>();
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="3d0df-155">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="3d0df-155">Kestrel options</span></span>

<span data-ttu-id="3d0df-156">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-156">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="3d0df-157">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="3d0df-157">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="3d0df-158">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="3d0df-158">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="3d0df-159">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="3d0df-159">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="3d0df-160">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-160">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3d0df-161">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置   ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-161">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="3d0df-162">使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-162">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="3d0df-163">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3d0df-163">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="3d0df-164">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="3d0df-164">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="3d0df-165">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="3d0df-165">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="3d0df-166">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中。</span><span class="sxs-lookup"><span data-stu-id="3d0df-166">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="3d0df-167">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3d0df-167">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="3d0df-168">在 Program.cs  中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="3d0df-168">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="3d0df-169">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-169">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="3d0df-170">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="3d0df-170">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="3d0df-171">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-171">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="3d0df-172">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="3d0df-172">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="3d0df-173">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="3d0df-173">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="3d0df-174">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="3d0df-174">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="3d0df-175">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-175">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="3d0df-176">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-176">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="3d0df-177">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-177">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="3d0df-178">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-178">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="3d0df-179">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="3d0df-179">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="3d0df-180">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="3d0df-180">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="3d0df-181">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="3d0df-181">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="3d0df-182">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-182">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="3d0df-183">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3d0df-183">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="3d0df-184">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-184">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="3d0df-185">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-185">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="3d0df-186">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="3d0df-186">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="3d0df-187">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="3d0df-187">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="3d0df-188">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="3d0df-188">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="3d0df-189">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="3d0df-189">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="3d0df-190">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="3d0df-190">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="3d0df-191">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="3d0df-191">A minimum rate also applies to the response.</span></span> <span data-ttu-id="3d0df-192">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="3d0df-192">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="3d0df-193">以下示例演示如何在 Program.cs 中配置最小数据速率  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-193">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="3d0df-194">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="3d0df-194">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="3d0df-195">用于 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>，因为鉴于协议支持请求多路复用，HTTP/2 通常不支持按请求修改速率限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-195">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="3d0df-196">不过，用于 HTTP/2 请求的 `HttpContext.Features` 中仍存在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>，因为仍可以通过将 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 设置为 `null`（甚至对于 HTTP/2 请求），按请求完全禁用  读取速率限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-196">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="3d0df-197">对于给定 HTTP/2 请求，尝试读取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或尝试将它设置为除 `null` 以外的值会导致 `NotSupportedException` 抛出。</span><span class="sxs-lookup"><span data-stu-id="3d0df-197">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="3d0df-198">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="3d0df-198">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="3d0df-199">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="3d0df-199">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="3d0df-200">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="3d0df-200">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="3d0df-201">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="3d0df-201">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="3d0df-202">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="3d0df-202">Maximum streams per connection</span></span>

<span data-ttu-id="3d0df-203">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="3d0df-203">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="3d0df-204">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="3d0df-204">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="3d0df-205">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="3d0df-205">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="3d0df-206">标题表大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-206">Header table size</span></span>

<span data-ttu-id="3d0df-207">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="3d0df-207">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="3d0df-208">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="3d0df-208">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="3d0df-209">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-209">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="3d0df-210">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="3d0df-210">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="3d0df-211">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-211">Maximum frame size</span></span>

<span data-ttu-id="3d0df-212">`Http2.MaxFrameSize` 表示服务器接收或发送的 HTTP/2 连接帧有效负载的最大允许大小。</span><span class="sxs-lookup"><span data-stu-id="3d0df-212">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="3d0df-213">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="3d0df-213">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="3d0df-214">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-214">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="3d0df-215">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-215">Maximum request header size</span></span>

<span data-ttu-id="3d0df-216">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-216">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="3d0df-217">此限制适用于名称和值的压缩和未压缩表示形式。</span><span class="sxs-lookup"><span data-stu-id="3d0df-217">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="3d0df-218">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-218">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="3d0df-219">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="3d0df-219">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="3d0df-220">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-220">Initial connection window size</span></span>

<span data-ttu-id="3d0df-221">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-221">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="3d0df-222">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-222">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="3d0df-223">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-223">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="3d0df-224">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-224">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="3d0df-225">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-225">Initial stream window size</span></span>

<span data-ttu-id="3d0df-226">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-226">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="3d0df-227">请求也受 `Http2.InitialConnectionWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-227">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="3d0df-228">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-228">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="3d0df-229">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-229">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="3d0df-230">同步 IO</span><span class="sxs-lookup"><span data-stu-id="3d0df-230">Synchronous IO</span></span>

<span data-ttu-id="3d0df-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 IO。</span><span class="sxs-lookup"><span data-stu-id="3d0df-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="3d0df-232">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-232">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="3d0df-233">大量的阻止同步 IO 操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="3d0df-233">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="3d0df-234">仅在使用不支持异步 IO 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-234">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="3d0df-235">下面的示例启用同步 IO：</span><span class="sxs-lookup"><span data-stu-id="3d0df-235">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="3d0df-236">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="3d0df-236">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="3d0df-237">终结点配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-237">Endpoint configuration</span></span>

<span data-ttu-id="3d0df-238">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="3d0df-238">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="3d0df-239">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="3d0df-239">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="3d0df-240">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="3d0df-240">Specify URLs using the:</span></span>

* <span data-ttu-id="3d0df-241">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="3d0df-241">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="3d0df-242">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3d0df-242">`--urls` command-line argument.</span></span>
* <span data-ttu-id="3d0df-243">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="3d0df-243">`urls` host configuration key.</span></span>
* <span data-ttu-id="3d0df-244">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="3d0df-244">`UseUrls` extension method.</span></span>

<span data-ttu-id="3d0df-245">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-245">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="3d0df-246">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000; http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-246">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="3d0df-247">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-247">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="3d0df-248">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="3d0df-248">A development certificate is created:</span></span>

* <span data-ttu-id="3d0df-249">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="3d0df-249">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="3d0df-250">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-250">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="3d0df-251">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-251">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="3d0df-252">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-252">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3d0df-253">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-253">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="3d0df-254">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-254">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="3d0df-255">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-255">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="3d0df-256">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="3d0df-256">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="3d0df-257">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-257">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="3d0df-258">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-258">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="3d0df-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="3d0df-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="3d0df-260">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-260">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="3d0df-261">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-261">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="3d0df-262">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="3d0df-262">Configure(IConfiguration)</span></span>

<span data-ttu-id="3d0df-263">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-263">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="3d0df-264">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="3d0df-264">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="3d0df-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="3d0df-265">ListenOptions.UseHttps</span></span>

<span data-ttu-id="3d0df-266">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-266">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="3d0df-267">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="3d0df-267">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="3d0df-268">`UseHttps` &ndash; 将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-268">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="3d0df-269">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3d0df-269">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="3d0df-270">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="3d0df-270">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="3d0df-271">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="3d0df-271">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="3d0df-272">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="3d0df-272">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="3d0df-273">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-273">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="3d0df-274">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-274">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="3d0df-275">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="3d0df-275">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="3d0df-276">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="3d0df-276">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="3d0df-277">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-277">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="3d0df-278">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-278">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="3d0df-279">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-279">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="3d0df-280">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-280">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="3d0df-281">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-281">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="3d0df-282">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-282">Supported configurations described next:</span></span>

* <span data-ttu-id="3d0df-283">无配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-283">No configuration</span></span>
* <span data-ttu-id="3d0df-284">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="3d0df-284">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="3d0df-285">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="3d0df-285">Change the defaults in code</span></span>

<span data-ttu-id="3d0df-286">*无配置*</span><span class="sxs-lookup"><span data-stu-id="3d0df-286">*No configuration*</span></span>

<span data-ttu-id="3d0df-287">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-287">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="3d0df-288">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="3d0df-288">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="3d0df-289">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-289">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="3d0df-290">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="3d0df-290">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="3d0df-291">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-291">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="3d0df-292">在以下 appsettings.json 示例中  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-292">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="3d0df-293">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）  。</span><span class="sxs-lookup"><span data-stu-id="3d0df-293">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="3d0df-294">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书    。</span><span class="sxs-lookup"><span data-stu-id="3d0df-294">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="3d0df-295">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书   。</span><span class="sxs-lookup"><span data-stu-id="3d0df-295">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="3d0df-296">例如，可将 Certificates > Default 证书指定为   ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-296">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="3d0df-297">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="3d0df-297">Schema notes:</span></span>

* <span data-ttu-id="3d0df-298">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3d0df-298">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="3d0df-299">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="3d0df-299">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="3d0df-300">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="3d0df-300">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="3d0df-301">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-301">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="3d0df-302">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="3d0df-302">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="3d0df-303">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="3d0df-303">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="3d0df-304">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="3d0df-304">The `Certificate` section is optional.</span></span> <span data-ttu-id="3d0df-305">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-305">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="3d0df-306">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="3d0df-306">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="3d0df-307">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书     。</span><span class="sxs-lookup"><span data-stu-id="3d0df-307">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="3d0df-308">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="3d0df-308">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="3d0df-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="3d0df-310">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="3d0df-310">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="3d0df-311">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-311">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="3d0df-312">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-312">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="3d0df-313">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-313">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="3d0df-314">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="3d0df-314">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="3d0df-315">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="3d0df-315">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="3d0df-316">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-316">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="3d0df-317">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="3d0df-317">*Change the defaults in code*</span></span>

<span data-ttu-id="3d0df-318">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="3d0df-319">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-319">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="3d0df-320">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="3d0df-320">*Kestrel support for SNI*</span></span>

<span data-ttu-id="3d0df-321">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="3d0df-321">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="3d0df-322">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-322">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="3d0df-323">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="3d0df-323">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="3d0df-324">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="3d0df-324">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="3d0df-325">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-325">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="3d0df-326">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="3d0df-326">SNI support requires:</span></span>

* <span data-ttu-id="3d0df-327">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="3d0df-327">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="3d0df-328">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-328">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="3d0df-329">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-329">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="3d0df-330">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="3d0df-330">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="3d0df-331">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-331">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="3d0df-332">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="3d0df-332">Bind to a TCP socket</span></span>

<span data-ttu-id="3d0df-333"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-333">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="3d0df-334">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-334">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="3d0df-335">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-335">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="3d0df-336">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3d0df-336">Bind to a Unix socket</span></span>

<span data-ttu-id="3d0df-337">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="3d0df-337">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="3d0df-338">端口 0</span><span class="sxs-lookup"><span data-stu-id="3d0df-338">Port 0</span></span>

<span data-ttu-id="3d0df-339">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-339">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="3d0df-340">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="3d0df-340">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="3d0df-341">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="3d0df-341">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="3d0df-342">限制</span><span class="sxs-lookup"><span data-stu-id="3d0df-342">Limitations</span></span>

<span data-ttu-id="3d0df-343">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="3d0df-343">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="3d0df-344">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="3d0df-344">`--urls` command-line argument</span></span>
* <span data-ttu-id="3d0df-345">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="3d0df-345">`urls` host configuration key</span></span>
* <span data-ttu-id="3d0df-346">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="3d0df-346">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="3d0df-347">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-347">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="3d0df-348">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="3d0df-348">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="3d0df-349">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-349">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="3d0df-350">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="3d0df-350">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="3d0df-351">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-351">IIS endpoint configuration</span></span>

<span data-ttu-id="3d0df-352">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="3d0df-352">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="3d0df-353">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="3d0df-353">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="3d0df-354">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="3d0df-354">ListenOptions.Protocols</span></span>

<span data-ttu-id="3d0df-355">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-355">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="3d0df-356">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-356">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="3d0df-357">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="3d0df-357">`HttpProtocols` enum value</span></span> | <span data-ttu-id="3d0df-358">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="3d0df-358">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="3d0df-359">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3d0df-359">HTTP/1.1 only.</span></span> <span data-ttu-id="3d0df-360">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-360">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="3d0df-361">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3d0df-361">HTTP/2 only.</span></span> <span data-ttu-id="3d0df-362">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-362">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="3d0df-363">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3d0df-363">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="3d0df-364">HTTP/2 要求客户端在 TLS [应用层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手过程中选择 HTTP/2；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3d0df-364">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="3d0df-365">任何终结点的默认 `ListenOptions.Protocols` 值都为 `HttpProtocols.Http1AndHttp2`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-365">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="3d0df-366">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="3d0df-366">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="3d0df-367">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3d0df-367">TLS version 1.2 or later</span></span>
* <span data-ttu-id="3d0df-368">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="3d0df-368">Renegotiation disabled</span></span>
* <span data-ttu-id="3d0df-369">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="3d0df-369">Compression disabled</span></span>
* <span data-ttu-id="3d0df-370">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="3d0df-370">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="3d0df-371">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash;最小 224 位</span><span class="sxs-lookup"><span data-stu-id="3d0df-371">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="3d0df-372">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="3d0df-372">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="3d0df-373">密码套件未列入阻止列表</span><span class="sxs-lookup"><span data-stu-id="3d0df-373">Cipher suite not blacklisted</span></span>

<span data-ttu-id="3d0df-374">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="3d0df-374">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="3d0df-375">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="3d0df-375">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="3d0df-376">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="3d0df-376">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="3d0df-377">使用连接中间件，针对特定密码的每个连接筛选 TLS 握手（如有必要）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-377">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="3d0df-378">下面的示例针对应用不支持的任何密码算法引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="3d0df-378">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="3d0df-379">或者，定义 [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 并将其与可接受的密码套件列表进行比较。</span><span class="sxs-lookup"><span data-stu-id="3d0df-379">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="3d0df-380">没有哪种加密是使用 [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 密码算法。</span><span class="sxs-lookup"><span data-stu-id="3d0df-380">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="3d0df-381">连接筛选也可以通过 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda 进行配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-381">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="3d0df-382">在 Linux 上，<xref:System.Net.Security.CipherSuitesPolicy> 可用于针对每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="3d0df-382">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="3d0df-383">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="3d0df-383">*Set the protocol from configuration*</span></span>

<span data-ttu-id="3d0df-384">`CreateDefaultBuilder` 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-384">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="3d0df-385">以下 appsettings.json 示例将 HTTP/1.1 建立为所有终结点的默认连接协议  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-385">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="3d0df-386">以下 appsettings.json 示例将 HTTP/1.1 建立为所有指定终结点的连接协议  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-386">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="3d0df-387">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-387">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="3d0df-388">传输配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-388">Transport configuration</span></span>

<span data-ttu-id="3d0df-389">对于需要使用 Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) 的项目：</span><span class="sxs-lookup"><span data-stu-id="3d0df-389">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="3d0df-390">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="3d0df-390">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="3d0df-391">调用 `IWebHostBuilder` 上的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="3d0df-391">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="3d0df-392">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="3d0df-392">URL prefixes</span></span>

<span data-ttu-id="3d0df-393">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="3d0df-393">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="3d0df-394">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="3d0df-394">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="3d0df-395">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-395">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="3d0df-396">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="3d0df-396">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="3d0df-397">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="3d0df-397">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="3d0df-398">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="3d0df-398">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="3d0df-399">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="3d0df-399">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="3d0df-400">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="3d0df-400">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="3d0df-401">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="3d0df-401">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="3d0df-402">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="3d0df-402">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="3d0df-403">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="3d0df-403">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="3d0df-404">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-404">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="3d0df-405">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="3d0df-405">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="3d0df-406">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-406">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="3d0df-407">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="3d0df-407">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="3d0df-408">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="3d0df-408">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="3d0df-409">主机筛选</span><span class="sxs-lookup"><span data-stu-id="3d0df-409">Host filtering</span></span>

<span data-ttu-id="3d0df-410">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="3d0df-410">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="3d0df-411">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="3d0df-411">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="3d0df-412">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="3d0df-412">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="3d0df-413">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="3d0df-413">`Host` headers aren't validated.</span></span>

<span data-ttu-id="3d0df-414">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="3d0df-414">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="3d0df-415">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包为 ASP.NET Core 应用隐式提供。</span><span class="sxs-lookup"><span data-stu-id="3d0df-415">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="3d0df-416">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="3d0df-416">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="3d0df-417">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="3d0df-417">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="3d0df-418">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键   。</span><span class="sxs-lookup"><span data-stu-id="3d0df-418">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="3d0df-419">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="3d0df-419">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="3d0df-420">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-420">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="3d0df-421">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="3d0df-421">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="3d0df-422">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="3d0df-422">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="3d0df-423">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3d0df-423">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="3d0df-424">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3d0df-424">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="3d0df-425">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="3d0df-425">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3d0df-426">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-426">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="3d0df-427">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="3d0df-427">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="3d0df-428">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="3d0df-428">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="3d0df-429">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3d0df-429">HTTPS</span></span>
* <span data-ttu-id="3d0df-430">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="3d0df-430">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="3d0df-431">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3d0df-431">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="3d0df-432">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="3d0df-432">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="3d0df-433">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3d0df-433">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="3d0df-434">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-434">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="3d0df-435">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3d0df-435">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="3d0df-436">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="3d0df-436">HTTP/2 support</span></span>

<span data-ttu-id="3d0df-437">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="3d0df-437">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="3d0df-438">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="3d0df-438">Operating system&dagger;</span></span>
  * <span data-ttu-id="3d0df-439">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="3d0df-439">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="3d0df-440">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="3d0df-440">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="3d0df-441">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3d0df-441">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="3d0df-442">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="3d0df-442">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="3d0df-443">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="3d0df-443">TLS 1.2 or later connection</span></span>

<span data-ttu-id="3d0df-444">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3d0df-444">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="3d0df-445">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="3d0df-445">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="3d0df-446">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="3d0df-446">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="3d0df-447">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="3d0df-447">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="3d0df-448">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-448">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="3d0df-449">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3d0df-449">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="3d0df-450">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="3d0df-450">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="3d0df-451">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="3d0df-451">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="3d0df-452">可以单独使用 Kestrel，也可以将其与反向代理服务器  （如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-452">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="3d0df-453">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-453">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="3d0df-454">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="3d0df-454">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="3d0df-456">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-456">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="3d0df-458">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-458">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="3d0df-459">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-459">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="3d0df-460">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-460">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="3d0df-461">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-461">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="3d0df-462">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="3d0df-462">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="3d0df-463">反向代理：</span><span class="sxs-lookup"><span data-stu-id="3d0df-463">A reverse proxy:</span></span>

* <span data-ttu-id="3d0df-464">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-464">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="3d0df-465">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="3d0df-465">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="3d0df-466">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="3d0df-466">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="3d0df-467">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-467">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="3d0df-468">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="3d0df-468">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="3d0df-469">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-469">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="3d0df-470">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="3d0df-470">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="3d0df-471">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="3d0df-471">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3d0df-472">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-472">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="3d0df-473">在 Program.cs  中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="3d0df-473">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="3d0df-474">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”  部分。</span><span class="sxs-lookup"><span data-stu-id="3d0df-474">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="3d0df-475">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="3d0df-475">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="3d0df-476">如果应用未调用 `CreateDefaultBuilder` 来设置主机，请在调用 `ConfigureKestrel` 之前  先调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="3d0df-476">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="3d0df-477">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="3d0df-477">Kestrel options</span></span>

<span data-ttu-id="3d0df-478">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-478">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="3d0df-479">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="3d0df-479">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="3d0df-480">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="3d0df-480">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="3d0df-481">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="3d0df-481">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="3d0df-482">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-482">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3d0df-483">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置   ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-483">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="3d0df-484">使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-484">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="3d0df-485">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3d0df-485">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="3d0df-486">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="3d0df-486">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="3d0df-487">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="3d0df-487">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="3d0df-488">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中。</span><span class="sxs-lookup"><span data-stu-id="3d0df-488">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="3d0df-489">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3d0df-489">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="3d0df-490">在 Program.cs  中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="3d0df-490">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="3d0df-491">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-491">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="3d0df-492">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="3d0df-492">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="3d0df-493">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-493">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="3d0df-494">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="3d0df-494">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="3d0df-495">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="3d0df-495">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="3d0df-496">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="3d0df-496">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="3d0df-497">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-497">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="3d0df-498">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-498">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="3d0df-499">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-499">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="3d0df-500">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-500">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="3d0df-501">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="3d0df-501">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="3d0df-502">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="3d0df-502">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="3d0df-503">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="3d0df-503">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="3d0df-504">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-504">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="3d0df-505">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3d0df-505">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="3d0df-506">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-506">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="3d0df-507">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-507">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="3d0df-508">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="3d0df-508">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="3d0df-509">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="3d0df-509">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="3d0df-510">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="3d0df-510">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="3d0df-511">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="3d0df-511">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="3d0df-512">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="3d0df-512">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="3d0df-513">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="3d0df-513">A minimum rate also applies to the response.</span></span> <span data-ttu-id="3d0df-514">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="3d0df-514">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="3d0df-515">以下示例演示如何在 Program.cs 中配置最小数据速率  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-515">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="3d0df-516">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="3d0df-516">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="3d0df-517">由于协议支持请求多路复用，HTTP/2 不支持基于每个请求修改速率限制，因此 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的速率特性。</span><span class="sxs-lookup"><span data-stu-id="3d0df-517">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="3d0df-518">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="3d0df-518">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="3d0df-519">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="3d0df-519">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="3d0df-520">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="3d0df-520">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="3d0df-521">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="3d0df-521">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="3d0df-522">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="3d0df-522">Maximum streams per connection</span></span>

<span data-ttu-id="3d0df-523">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="3d0df-523">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="3d0df-524">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="3d0df-524">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="3d0df-525">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="3d0df-525">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="3d0df-526">标题表大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-526">Header table size</span></span>

<span data-ttu-id="3d0df-527">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="3d0df-527">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="3d0df-528">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="3d0df-528">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="3d0df-529">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-529">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="3d0df-530">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="3d0df-530">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="3d0df-531">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-531">Maximum frame size</span></span>

<span data-ttu-id="3d0df-532">`Http2.MaxFrameSize` 指示要接收的 HTTP/2 连接帧有效负载的最大大小。</span><span class="sxs-lookup"><span data-stu-id="3d0df-532">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="3d0df-533">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="3d0df-533">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="3d0df-534">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-534">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="3d0df-535">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-535">Maximum request header size</span></span>

<span data-ttu-id="3d0df-536">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-536">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="3d0df-537">此限制同时适用于压缩和未压缩表示形式中的名称和值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-537">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="3d0df-538">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-538">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="3d0df-539">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="3d0df-539">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="3d0df-540">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-540">Initial connection window size</span></span>

<span data-ttu-id="3d0df-541">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-541">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="3d0df-542">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-542">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="3d0df-543">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-543">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="3d0df-544">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-544">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="3d0df-545">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-545">Initial stream window size</span></span>

<span data-ttu-id="3d0df-546">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-546">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="3d0df-547">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-547">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="3d0df-548">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-548">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="3d0df-549">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-549">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="3d0df-550">同步 IO</span><span class="sxs-lookup"><span data-stu-id="3d0df-550">Synchronous IO</span></span>

<span data-ttu-id="3d0df-551"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 IO。</span><span class="sxs-lookup"><span data-stu-id="3d0df-551"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="3d0df-552">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-552">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="3d0df-553">大量的阻止同步 IO 操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="3d0df-553">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="3d0df-554">仅在使用不支持异步 IO 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-554">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="3d0df-555">下面的示例启用同步 IO：</span><span class="sxs-lookup"><span data-stu-id="3d0df-555">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="3d0df-556">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="3d0df-556">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="3d0df-557">终结点配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-557">Endpoint configuration</span></span>

<span data-ttu-id="3d0df-558">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="3d0df-558">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="3d0df-559">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="3d0df-559">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="3d0df-560">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="3d0df-560">Specify URLs using the:</span></span>

* <span data-ttu-id="3d0df-561">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="3d0df-561">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="3d0df-562">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3d0df-562">`--urls` command-line argument.</span></span>
* <span data-ttu-id="3d0df-563">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="3d0df-563">`urls` host configuration key.</span></span>
* <span data-ttu-id="3d0df-564">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="3d0df-564">`UseUrls` extension method.</span></span>

<span data-ttu-id="3d0df-565">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-565">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="3d0df-566">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000; http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-566">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="3d0df-567">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-567">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="3d0df-568">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="3d0df-568">A development certificate is created:</span></span>

* <span data-ttu-id="3d0df-569">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="3d0df-569">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="3d0df-570">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-570">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="3d0df-571">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-571">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="3d0df-572">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-572">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3d0df-573">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-573">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="3d0df-574">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-574">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="3d0df-575">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-575">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="3d0df-576">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="3d0df-576">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="3d0df-577">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-577">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="3d0df-578">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-578">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="3d0df-579">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="3d0df-579">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="3d0df-580">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-580">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="3d0df-581">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-581">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="3d0df-582">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="3d0df-582">Configure(IConfiguration)</span></span>

<span data-ttu-id="3d0df-583">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-583">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="3d0df-584">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="3d0df-584">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="3d0df-585">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="3d0df-585">ListenOptions.UseHttps</span></span>

<span data-ttu-id="3d0df-586">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-586">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="3d0df-587">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="3d0df-587">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="3d0df-588">`UseHttps` &ndash; 将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-588">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="3d0df-589">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3d0df-589">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="3d0df-590">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="3d0df-590">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="3d0df-591">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="3d0df-591">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="3d0df-592">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="3d0df-592">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="3d0df-593">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-593">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="3d0df-594">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-594">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="3d0df-595">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="3d0df-595">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="3d0df-596">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="3d0df-596">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="3d0df-597">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-597">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="3d0df-598">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-598">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="3d0df-599">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-599">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="3d0df-600">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-600">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="3d0df-601">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-601">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="3d0df-602">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-602">Supported configurations described next:</span></span>

* <span data-ttu-id="3d0df-603">无配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-603">No configuration</span></span>
* <span data-ttu-id="3d0df-604">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="3d0df-604">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="3d0df-605">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="3d0df-605">Change the defaults in code</span></span>

<span data-ttu-id="3d0df-606">*无配置*</span><span class="sxs-lookup"><span data-stu-id="3d0df-606">*No configuration*</span></span>

<span data-ttu-id="3d0df-607">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-607">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="3d0df-608">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="3d0df-608">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="3d0df-609">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-609">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="3d0df-610">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="3d0df-610">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="3d0df-611">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-611">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="3d0df-612">在以下 appsettings.json 示例中  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-612">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="3d0df-613">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）  。</span><span class="sxs-lookup"><span data-stu-id="3d0df-613">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="3d0df-614">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书    。</span><span class="sxs-lookup"><span data-stu-id="3d0df-614">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="3d0df-615">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书   。</span><span class="sxs-lookup"><span data-stu-id="3d0df-615">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="3d0df-616">例如，可将 Certificates > Default 证书指定为   ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-616">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="3d0df-617">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="3d0df-617">Schema notes:</span></span>

* <span data-ttu-id="3d0df-618">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3d0df-618">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="3d0df-619">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="3d0df-619">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="3d0df-620">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="3d0df-620">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="3d0df-621">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-621">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="3d0df-622">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="3d0df-622">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="3d0df-623">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="3d0df-623">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="3d0df-624">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="3d0df-624">The `Certificate` section is optional.</span></span> <span data-ttu-id="3d0df-625">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-625">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="3d0df-626">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="3d0df-626">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="3d0df-627">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书     。</span><span class="sxs-lookup"><span data-stu-id="3d0df-627">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="3d0df-628">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="3d0df-628">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="3d0df-629">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-629">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="3d0df-630">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="3d0df-630">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="3d0df-631">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-631">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="3d0df-632">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-632">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="3d0df-633">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-633">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="3d0df-634">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="3d0df-634">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="3d0df-635">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="3d0df-635">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="3d0df-636">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-636">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="3d0df-637">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="3d0df-637">*Change the defaults in code*</span></span>

<span data-ttu-id="3d0df-638">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-638">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="3d0df-639">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-639">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="3d0df-640">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="3d0df-640">*Kestrel support for SNI*</span></span>

<span data-ttu-id="3d0df-641">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="3d0df-641">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="3d0df-642">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-642">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="3d0df-643">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="3d0df-643">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="3d0df-644">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="3d0df-644">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="3d0df-645">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-645">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="3d0df-646">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="3d0df-646">SNI support requires:</span></span>

* <span data-ttu-id="3d0df-647">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="3d0df-647">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="3d0df-648">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-648">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="3d0df-649">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-649">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="3d0df-650">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="3d0df-650">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="3d0df-651">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-651">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="3d0df-652">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="3d0df-652">Bind to a TCP socket</span></span>

<span data-ttu-id="3d0df-653"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-653">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="3d0df-654">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-654">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="3d0df-655">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-655">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="3d0df-656">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3d0df-656">Bind to a Unix socket</span></span>

<span data-ttu-id="3d0df-657">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="3d0df-657">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="3d0df-658">端口 0</span><span class="sxs-lookup"><span data-stu-id="3d0df-658">Port 0</span></span>

<span data-ttu-id="3d0df-659">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-659">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="3d0df-660">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="3d0df-660">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="3d0df-661">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="3d0df-661">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="3d0df-662">限制</span><span class="sxs-lookup"><span data-stu-id="3d0df-662">Limitations</span></span>

<span data-ttu-id="3d0df-663">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="3d0df-663">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="3d0df-664">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="3d0df-664">`--urls` command-line argument</span></span>
* <span data-ttu-id="3d0df-665">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="3d0df-665">`urls` host configuration key</span></span>
* <span data-ttu-id="3d0df-666">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="3d0df-666">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="3d0df-667">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-667">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="3d0df-668">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="3d0df-668">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="3d0df-669">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-669">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="3d0df-670">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="3d0df-670">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="3d0df-671">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-671">IIS endpoint configuration</span></span>

<span data-ttu-id="3d0df-672">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="3d0df-672">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="3d0df-673">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="3d0df-673">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="3d0df-674">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="3d0df-674">ListenOptions.Protocols</span></span>

<span data-ttu-id="3d0df-675">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-675">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="3d0df-676">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-676">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="3d0df-677">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="3d0df-677">`HttpProtocols` enum value</span></span> | <span data-ttu-id="3d0df-678">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="3d0df-678">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="3d0df-679">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3d0df-679">HTTP/1.1 only.</span></span> <span data-ttu-id="3d0df-680">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-680">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="3d0df-681">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3d0df-681">HTTP/2 only.</span></span> <span data-ttu-id="3d0df-682">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-682">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="3d0df-683">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3d0df-683">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="3d0df-684">HTTP/2 需要 TLS 和[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3d0df-684">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="3d0df-685">默认协议是 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3d0df-685">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="3d0df-686">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="3d0df-686">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="3d0df-687">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3d0df-687">TLS version 1.2 or later</span></span>
* <span data-ttu-id="3d0df-688">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="3d0df-688">Renegotiation disabled</span></span>
* <span data-ttu-id="3d0df-689">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="3d0df-689">Compression disabled</span></span>
* <span data-ttu-id="3d0df-690">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="3d0df-690">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="3d0df-691">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash;最小 224 位</span><span class="sxs-lookup"><span data-stu-id="3d0df-691">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="3d0df-692">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="3d0df-692">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="3d0df-693">密码套件未列入阻止列表</span><span class="sxs-lookup"><span data-stu-id="3d0df-693">Cipher suite not blacklisted</span></span>

<span data-ttu-id="3d0df-694">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="3d0df-694">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="3d0df-695">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="3d0df-695">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="3d0df-696">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="3d0df-696">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="3d0df-697">（可选）创建 `IConnectionAdapter` 实现，以针对特定密码的每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="3d0df-697">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="3d0df-698">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="3d0df-698">*Set the protocol from configuration*</span></span>

<span data-ttu-id="3d0df-699"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-699"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="3d0df-700">在以下 appsettings.json  示例中，为 Kestrel 的所有终结点建立默认的连接协议（HTTP/1.1 和 HTTP/2）：</span><span class="sxs-lookup"><span data-stu-id="3d0df-700">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="3d0df-701">以下配置文件示例为特定终结点建立了连接协议：</span><span class="sxs-lookup"><span data-stu-id="3d0df-701">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="3d0df-702">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-702">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="3d0df-703">传输配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-703">Transport configuration</span></span>

<span data-ttu-id="3d0df-704">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="3d0df-704">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="3d0df-705">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="3d0df-705">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="3d0df-706">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="3d0df-706">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="3d0df-707">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="3d0df-707">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="3d0df-708">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="3d0df-708">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="3d0df-709">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="3d0df-709">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="3d0df-710">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="3d0df-710">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="3d0df-711">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="3d0df-711">URL prefixes</span></span>

<span data-ttu-id="3d0df-712">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="3d0df-712">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="3d0df-713">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="3d0df-713">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="3d0df-714">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-714">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="3d0df-715">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="3d0df-715">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="3d0df-716">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="3d0df-716">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="3d0df-717">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="3d0df-717">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="3d0df-718">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="3d0df-718">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="3d0df-719">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="3d0df-719">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="3d0df-720">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="3d0df-720">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="3d0df-721">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="3d0df-721">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="3d0df-722">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="3d0df-722">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="3d0df-723">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-723">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="3d0df-724">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="3d0df-724">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="3d0df-725">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-725">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="3d0df-726">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="3d0df-726">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="3d0df-727">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="3d0df-727">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="3d0df-728">主机筛选</span><span class="sxs-lookup"><span data-stu-id="3d0df-728">Host filtering</span></span>

<span data-ttu-id="3d0df-729">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="3d0df-729">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="3d0df-730">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="3d0df-730">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="3d0df-731">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="3d0df-731">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="3d0df-732">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="3d0df-732">`Host` headers aren't validated.</span></span>

<span data-ttu-id="3d0df-733">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="3d0df-733">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="3d0df-734">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-734">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="3d0df-735">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="3d0df-735">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="3d0df-736">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="3d0df-736">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="3d0df-737">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键   。</span><span class="sxs-lookup"><span data-stu-id="3d0df-737">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="3d0df-738">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="3d0df-738">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="3d0df-739">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-739">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="3d0df-740">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="3d0df-740">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="3d0df-741">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="3d0df-741">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="3d0df-742">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3d0df-742">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="3d0df-743">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3d0df-743">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="3d0df-744">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="3d0df-744">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3d0df-745">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-745">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="3d0df-746">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="3d0df-746">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="3d0df-747">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="3d0df-747">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="3d0df-748">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3d0df-748">HTTPS</span></span>
* <span data-ttu-id="3d0df-749">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="3d0df-749">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="3d0df-750">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3d0df-750">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="3d0df-751">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-751">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="3d0df-752">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3d0df-752">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="3d0df-753">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="3d0df-753">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="3d0df-754">可以单独使用 Kestrel，也可以将其与反向代理服务器  （如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-754">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="3d0df-755">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-755">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="3d0df-756">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="3d0df-756">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="3d0df-758">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-758">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="3d0df-760">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-760">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="3d0df-761">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-761">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="3d0df-762">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-762">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="3d0df-763">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-763">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="3d0df-764">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="3d0df-764">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="3d0df-765">反向代理：</span><span class="sxs-lookup"><span data-stu-id="3d0df-765">A reverse proxy:</span></span>

* <span data-ttu-id="3d0df-766">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-766">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="3d0df-767">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="3d0df-767">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="3d0df-768">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="3d0df-768">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="3d0df-769">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-769">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="3d0df-770">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="3d0df-770">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="3d0df-771">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-771">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="3d0df-772">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="3d0df-772">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="3d0df-773">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="3d0df-773">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3d0df-774">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-774">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="3d0df-775">在 Program.cs  中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="3d0df-775">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="3d0df-776">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="3d0df-776">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="3d0df-777">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”  部分。</span><span class="sxs-lookup"><span data-stu-id="3d0df-777">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="3d0df-778">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="3d0df-778">Kestrel options</span></span>

<span data-ttu-id="3d0df-779">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-779">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="3d0df-780">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="3d0df-780">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="3d0df-781">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="3d0df-781">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="3d0df-782">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="3d0df-782">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="3d0df-783">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-783">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3d0df-784">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置   ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-784">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="3d0df-785">使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-785">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="3d0df-786">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3d0df-786">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="3d0df-787">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="3d0df-787">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="3d0df-788">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="3d0df-788">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="3d0df-789">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中。</span><span class="sxs-lookup"><span data-stu-id="3d0df-789">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="3d0df-790">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3d0df-790">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="3d0df-791">在 Program.cs  中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="3d0df-791">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="3d0df-792">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-792">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="3d0df-793">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="3d0df-793">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="3d0df-794">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-794">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="3d0df-795">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="3d0df-795">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="3d0df-796">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="3d0df-796">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="3d0df-797">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="3d0df-797">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="3d0df-798">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-798">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="3d0df-799">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-799">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="3d0df-800">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-800">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="3d0df-801">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="3d0df-801">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="3d0df-802">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="3d0df-802">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="3d0df-803">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="3d0df-803">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="3d0df-804">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="3d0df-804">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="3d0df-805">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-805">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="3d0df-806">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3d0df-806">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="3d0df-807">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-807">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="3d0df-808">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="3d0df-808">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="3d0df-809">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="3d0df-809">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="3d0df-810">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="3d0df-810">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="3d0df-811">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="3d0df-811">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="3d0df-812">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="3d0df-812">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="3d0df-813">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="3d0df-813">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="3d0df-814">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="3d0df-814">A minimum rate also applies to the response.</span></span> <span data-ttu-id="3d0df-815">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="3d0df-815">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="3d0df-816">以下示例演示如何在 Program.cs 中配置最小数据速率  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-816">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="3d0df-817">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="3d0df-817">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="3d0df-818">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="3d0df-818">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="3d0df-819">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="3d0df-819">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="3d0df-820">同步 IO</span><span class="sxs-lookup"><span data-stu-id="3d0df-820">Synchronous IO</span></span>

<span data-ttu-id="3d0df-821"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 IO。</span><span class="sxs-lookup"><span data-stu-id="3d0df-821"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="3d0df-822">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-822">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="3d0df-823">大量的阻止同步 IO 操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="3d0df-823">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="3d0df-824">仅在使用不支持异步 IO 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-824">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="3d0df-825">下面的示例禁用同步 IO：</span><span class="sxs-lookup"><span data-stu-id="3d0df-825">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="3d0df-826">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="3d0df-826">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="3d0df-827">终结点配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-827">Endpoint configuration</span></span>

<span data-ttu-id="3d0df-828">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="3d0df-828">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="3d0df-829">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="3d0df-829">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="3d0df-830">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="3d0df-830">Specify URLs using the:</span></span>

* <span data-ttu-id="3d0df-831">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="3d0df-831">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="3d0df-832">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3d0df-832">`--urls` command-line argument.</span></span>
* <span data-ttu-id="3d0df-833">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="3d0df-833">`urls` host configuration key.</span></span>
* <span data-ttu-id="3d0df-834">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="3d0df-834">`UseUrls` extension method.</span></span>

<span data-ttu-id="3d0df-835">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-835">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="3d0df-836">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000; http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-836">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="3d0df-837">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-837">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="3d0df-838">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="3d0df-838">A development certificate is created:</span></span>

* <span data-ttu-id="3d0df-839">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="3d0df-839">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="3d0df-840">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-840">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="3d0df-841">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-841">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="3d0df-842">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-842">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3d0df-843">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-843">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="3d0df-844">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-844">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="3d0df-845">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-845">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="3d0df-846">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="3d0df-846">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="3d0df-847">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-847">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="3d0df-848">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-848">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="3d0df-849">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="3d0df-849">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="3d0df-850">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-850">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="3d0df-851">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-851">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="3d0df-852">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="3d0df-852">Configure(IConfiguration)</span></span>

<span data-ttu-id="3d0df-853">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3d0df-853">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="3d0df-854">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="3d0df-854">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="3d0df-855">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="3d0df-855">ListenOptions.UseHttps</span></span>

<span data-ttu-id="3d0df-856">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-856">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="3d0df-857">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="3d0df-857">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="3d0df-858">`UseHttps` &ndash; 将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-858">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="3d0df-859">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3d0df-859">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="3d0df-860">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="3d0df-860">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="3d0df-861">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="3d0df-861">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="3d0df-862">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="3d0df-862">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="3d0df-863">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-863">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="3d0df-864">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-864">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="3d0df-865">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="3d0df-865">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="3d0df-866">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="3d0df-866">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="3d0df-867">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-867">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="3d0df-868">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-868">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="3d0df-869">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-869">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="3d0df-870">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-870">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="3d0df-871">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-871">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="3d0df-872">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-872">Supported configurations described next:</span></span>

* <span data-ttu-id="3d0df-873">无配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-873">No configuration</span></span>
* <span data-ttu-id="3d0df-874">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="3d0df-874">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="3d0df-875">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="3d0df-875">Change the defaults in code</span></span>

<span data-ttu-id="3d0df-876">*无配置*</span><span class="sxs-lookup"><span data-stu-id="3d0df-876">*No configuration*</span></span>

<span data-ttu-id="3d0df-877">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-877">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="3d0df-878">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="3d0df-878">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="3d0df-879">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-879">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="3d0df-880">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="3d0df-880">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="3d0df-881">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-881">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="3d0df-882">在以下 appsettings.json 示例中  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-882">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="3d0df-883">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）  。</span><span class="sxs-lookup"><span data-stu-id="3d0df-883">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="3d0df-884">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书    。</span><span class="sxs-lookup"><span data-stu-id="3d0df-884">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="3d0df-885">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书   。</span><span class="sxs-lookup"><span data-stu-id="3d0df-885">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="3d0df-886">例如，可将 Certificates > Default 证书指定为   ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-886">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="3d0df-887">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="3d0df-887">Schema notes:</span></span>

* <span data-ttu-id="3d0df-888">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3d0df-888">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="3d0df-889">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="3d0df-889">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="3d0df-890">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="3d0df-890">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="3d0df-891">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-891">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="3d0df-892">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="3d0df-892">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="3d0df-893">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="3d0df-893">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="3d0df-894">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="3d0df-894">The `Certificate` section is optional.</span></span> <span data-ttu-id="3d0df-895">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="3d0df-895">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="3d0df-896">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="3d0df-896">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="3d0df-897">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书     。</span><span class="sxs-lookup"><span data-stu-id="3d0df-897">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="3d0df-898">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="3d0df-898">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="3d0df-899">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-899">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="3d0df-900">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="3d0df-900">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="3d0df-901">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-901">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="3d0df-902">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-902">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="3d0df-903">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-903">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="3d0df-904">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="3d0df-904">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="3d0df-905">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="3d0df-905">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="3d0df-906">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-906">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="3d0df-907">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="3d0df-907">*Change the defaults in code*</span></span>

<span data-ttu-id="3d0df-908">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-908">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="3d0df-909">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-909">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="3d0df-910">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="3d0df-910">*Kestrel support for SNI*</span></span>

<span data-ttu-id="3d0df-911">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="3d0df-911">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="3d0df-912">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-912">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="3d0df-913">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="3d0df-913">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="3d0df-914">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="3d0df-914">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="3d0df-915">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="3d0df-915">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="3d0df-916">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="3d0df-916">SNI support requires:</span></span>

* <span data-ttu-id="3d0df-917">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="3d0df-917">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="3d0df-918">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-918">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="3d0df-919">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3d0df-919">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="3d0df-920">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="3d0df-920">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="3d0df-921">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-921">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="3d0df-922">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="3d0df-922">Bind to a TCP socket</span></span>

<span data-ttu-id="3d0df-923"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="3d0df-923">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="3d0df-924">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-924">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="3d0df-925">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="3d0df-925">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="3d0df-926">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3d0df-926">Bind to a Unix socket</span></span>

<span data-ttu-id="3d0df-927">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="3d0df-927">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

### <a name="port-0"></a><span data-ttu-id="3d0df-928">端口 0</span><span class="sxs-lookup"><span data-stu-id="3d0df-928">Port 0</span></span>

<span data-ttu-id="3d0df-929">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-929">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="3d0df-930">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="3d0df-930">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="3d0df-931">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="3d0df-931">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="3d0df-932">限制</span><span class="sxs-lookup"><span data-stu-id="3d0df-932">Limitations</span></span>

<span data-ttu-id="3d0df-933">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="3d0df-933">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="3d0df-934">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="3d0df-934">`--urls` command-line argument</span></span>
* <span data-ttu-id="3d0df-935">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="3d0df-935">`urls` host configuration key</span></span>
* <span data-ttu-id="3d0df-936">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="3d0df-936">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="3d0df-937">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="3d0df-937">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="3d0df-938">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="3d0df-938">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="3d0df-939">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-939">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="3d0df-940">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="3d0df-940">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="3d0df-941">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-941">IIS endpoint configuration</span></span>

<span data-ttu-id="3d0df-942">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="3d0df-942">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="3d0df-943">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="3d0df-943">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="3d0df-944">传输配置</span><span class="sxs-lookup"><span data-stu-id="3d0df-944">Transport configuration</span></span>

<span data-ttu-id="3d0df-945">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="3d0df-945">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="3d0df-946">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="3d0df-946">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="3d0df-947">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="3d0df-947">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="3d0df-948">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="3d0df-948">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="3d0df-949">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="3d0df-949">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="3d0df-950">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="3d0df-950">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="3d0df-951">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="3d0df-951">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="3d0df-952">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="3d0df-952">URL prefixes</span></span>

<span data-ttu-id="3d0df-953">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="3d0df-953">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="3d0df-954">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="3d0df-954">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="3d0df-955">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d0df-955">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="3d0df-956">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="3d0df-956">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="3d0df-957">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="3d0df-957">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="3d0df-958">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="3d0df-958">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="3d0df-959">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="3d0df-959">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="3d0df-960">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="3d0df-960">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="3d0df-961">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="3d0df-961">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="3d0df-962">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="3d0df-962">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="3d0df-963">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="3d0df-963">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="3d0df-964">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3d0df-964">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="3d0df-965">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="3d0df-965">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="3d0df-966">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="3d0df-966">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="3d0df-967">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="3d0df-967">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="3d0df-968">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="3d0df-968">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="3d0df-969">主机筛选</span><span class="sxs-lookup"><span data-stu-id="3d0df-969">Host filtering</span></span>

<span data-ttu-id="3d0df-970">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="3d0df-970">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="3d0df-971">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="3d0df-971">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="3d0df-972">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="3d0df-972">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="3d0df-973">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="3d0df-973">`Host` headers aren't validated.</span></span>

<span data-ttu-id="3d0df-974">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="3d0df-974">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="3d0df-975">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="3d0df-975">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="3d0df-976">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="3d0df-976">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="3d0df-977">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="3d0df-977">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="3d0df-978">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键   。</span><span class="sxs-lookup"><span data-stu-id="3d0df-978">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="3d0df-979">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="3d0df-979">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="3d0df-980">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="3d0df-980">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="3d0df-981">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="3d0df-981">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="3d0df-982">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="3d0df-982">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="3d0df-983">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3d0df-983">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="3d0df-984">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3d0df-984">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="3d0df-985">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="3d0df-985">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3d0df-986">其他资源</span><span class="sxs-lookup"><span data-stu-id="3d0df-986">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="3d0df-987">RFC 7230：消息语法和路由（5.4 节：主机）</span><span class="sxs-lookup"><span data-stu-id="3d0df-987">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
