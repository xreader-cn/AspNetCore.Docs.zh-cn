---
<span data-ttu-id="acc26-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-102">'Blazor'</span></span>
- <span data-ttu-id="acc26-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-103">'Identity'</span></span>
- <span data-ttu-id="acc26-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-105">'Razor'</span></span>
- <span data-ttu-id="acc26-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-106">'SignalR' uid:</span></span> 

---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="acc26-107">ASP.NET Core 中的 Kestrel Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="acc26-107">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="acc26-108">作者：[Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher) 和 [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="acc26-108">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="acc26-109">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="acc26-109">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="acc26-110">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="acc26-110">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="acc26-111">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="acc26-111">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="acc26-112">HTTPS</span><span class="sxs-lookup"><span data-stu-id="acc26-112">HTTPS</span></span>
* <span data-ttu-id="acc26-113">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="acc26-113">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="acc26-114">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="acc26-114">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="acc26-115">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="acc26-115">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="acc26-116">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acc26-116">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="acc26-117">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-117">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="acc26-118">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="acc26-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="acc26-119">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="acc26-119">HTTP/2 support</span></span>

<span data-ttu-id="acc26-120">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="acc26-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="acc26-121">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="acc26-121">Operating system&dagger;</span></span>
  * <span data-ttu-id="acc26-122">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="acc26-122">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="acc26-123">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="acc26-123">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="acc26-124">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="acc26-124">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="acc26-125">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="acc26-125">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="acc26-126">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="acc26-126">TLS 1.2 or later connection</span></span>

<span data-ttu-id="acc26-127">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acc26-127">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="acc26-128">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="acc26-128">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="acc26-129">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="acc26-129">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="acc26-130">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="acc26-130">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="acc26-131">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="acc26-131">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="acc26-132">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acc26-132">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="acc26-133">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="acc26-133">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="acc26-134">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="acc26-134">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="acc26-135">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="acc26-135">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="acc26-136">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-136">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="acc26-137">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="acc26-137">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="acc26-139">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-139">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="acc26-141">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-141">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="acc26-142">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-142">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="acc26-143">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="acc26-143">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="acc26-144">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-144">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="acc26-145">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="acc26-145">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="acc26-146">反向代理：</span><span class="sxs-lookup"><span data-stu-id="acc26-146">A reverse proxy:</span></span>

* <span data-ttu-id="acc26-147">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="acc26-147">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="acc26-148">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="acc26-148">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="acc26-149">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="acc26-149">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="acc26-150">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-150">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="acc26-151">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="acc26-151">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="acc26-152">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acc26-152">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="acc26-153">ASP.NET Core 应用中的 Kestrel</span><span class="sxs-lookup"><span data-stu-id="acc26-153">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="acc26-154">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-154">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="acc26-155">在“Program.cs”中，<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="acc26-155">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="acc26-156">有关生成主机的详细信息，请参阅 <xref:fundamentals/host/generic-host#set-up-a-host> 的“设置主机”和“默认生成器设置”部分 。</span><span class="sxs-lookup"><span data-stu-id="acc26-156">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="acc26-157">若要在调用 `ConfigureWebHostDefaults` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="acc26-157">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="acc26-158">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="acc26-158">Kestrel options</span></span>

<span data-ttu-id="acc26-159">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="acc26-159">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="acc26-160">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="acc26-160">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="acc26-161">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="acc26-161">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="acc26-162">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="acc26-162">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="acc26-163">在本文后面的示例中，Kestrel 选项是采用 C# 代码配置的。</span><span class="sxs-lookup"><span data-stu-id="acc26-163">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="acc26-164">还可以使用 [配置提供程序](xref:fundamentals/configuration/index)设置 Kestrel 选项。</span><span class="sxs-lookup"><span data-stu-id="acc26-164">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="acc26-165">例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置 ：</span><span class="sxs-lookup"><span data-stu-id="acc26-165">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="acc26-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 和 [终结点配置](#endpoint-configuration) 可以通过配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="acc26-167">其余的 Kestrel 配置必须采用 C# 代码进行配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-167">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="acc26-168">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="acc26-168">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="acc26-169">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="acc26-169">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="acc26-170">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="acc26-170">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="acc26-171">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="acc26-171">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="acc26-172">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="acc26-172">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="acc26-173">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="acc26-173">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="acc26-174">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="acc26-174">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="acc26-175">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="acc26-175">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="acc26-176">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="acc26-176">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="acc26-177">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="acc26-177">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="acc26-178">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="acc26-178">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="acc26-179">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="acc26-179">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="acc26-180">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="acc26-180">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="acc26-181">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-181">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="acc26-182">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-182">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="acc26-183">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="acc26-183">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="acc26-184">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="acc26-184">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="acc26-185">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="acc26-185">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="acc26-186">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="acc26-186">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="acc26-187">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="acc26-187">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="acc26-188">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="acc26-188">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="acc26-189">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="acc26-189">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="acc26-190">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-190">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="acc26-191">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-191">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="acc26-192">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="acc26-192">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="acc26-193">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="acc26-193">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="acc26-194">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="acc26-194">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="acc26-195">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="acc26-195">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="acc26-196">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="acc26-196">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="acc26-197">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-197">A minimum rate also applies to the response.</span></span> <span data-ttu-id="acc26-198">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="acc26-198">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="acc26-199">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="acc26-199">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="acc26-200">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="acc26-200">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="acc26-201">用于 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>，因为鉴于协议支持请求多路复用，HTTP/2 通常不支持按请求修改速率限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-201">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="acc26-202">不过，用于 HTTP/2 请求的 `HttpContext.Features` 中仍存在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>，因为仍可以通过将 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 设置为 `null`（甚至对于 HTTP/2 请求），按请求完全禁用读取速率限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-202">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="acc26-203">对于给定 HTTP/2 请求，尝试读取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或尝试将它设置为除 `null` 以外的值会导致 `NotSupportedException` 抛出。</span><span class="sxs-lookup"><span data-stu-id="acc26-203">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="acc26-204">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="acc26-204">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="acc26-205">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="acc26-205">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="acc26-206">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="acc26-206">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="acc26-207">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="acc26-207">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="acc26-208">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="acc26-208">Maximum streams per connection</span></span>

<span data-ttu-id="acc26-209">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="acc26-209">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="acc26-210">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="acc26-210">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="acc26-211">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="acc26-211">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="acc26-212">标题表大小</span><span class="sxs-lookup"><span data-stu-id="acc26-212">Header table size</span></span>

<span data-ttu-id="acc26-213">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="acc26-213">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="acc26-214">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="acc26-214">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="acc26-215">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="acc26-215">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="acc26-216">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="acc26-216">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="acc26-217">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="acc26-217">Maximum frame size</span></span>

<span data-ttu-id="acc26-218">`Http2.MaxFrameSize` 表示服务器接收或发送的 HTTP/2 连接帧有效负载的最大允许大小。</span><span class="sxs-lookup"><span data-stu-id="acc26-218">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="acc26-219">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="acc26-219">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="acc26-220">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="acc26-220">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="acc26-221">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="acc26-221">Maximum request header size</span></span>

<span data-ttu-id="acc26-222">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="acc26-222">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="acc26-223">此限制适用于名称和值的压缩和未压缩表示形式。</span><span class="sxs-lookup"><span data-stu-id="acc26-223">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="acc26-224">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="acc26-224">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="acc26-225">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="acc26-225">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="acc26-226">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="acc26-226">Initial connection window size</span></span>

<span data-ttu-id="acc26-227">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="acc26-227">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="acc26-228">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-228">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="acc26-229">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="acc26-229">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="acc26-230">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="acc26-230">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="acc26-231">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="acc26-231">Initial stream window size</span></span>

<span data-ttu-id="acc26-232">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="acc26-232">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="acc26-233">请求也受 `Http2.InitialConnectionWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-233">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="acc26-234">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="acc26-234">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="acc26-235">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="acc26-235">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="acc26-236">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="acc26-236">Synchronous I/O</span></span>

<span data-ttu-id="acc26-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="acc26-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="acc26-238">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="acc26-238">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="acc26-239">大量阻止同步 I/O 的操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-239">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="acc26-240">仅在使用不支持异步 I/O 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="acc26-240">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="acc26-241">以下示例会启用同步 I/O：</span><span class="sxs-lookup"><span data-stu-id="acc26-241">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="acc26-242">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="acc26-242">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="acc26-243">终结点配置</span><span class="sxs-lookup"><span data-stu-id="acc26-243">Endpoint configuration</span></span>

<span data-ttu-id="acc26-244">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="acc26-244">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="acc26-245">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="acc26-245">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="acc26-246">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="acc26-246">Specify URLs using the:</span></span>

* <span data-ttu-id="acc26-247">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="acc26-247">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="acc26-248">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="acc26-248">`--urls` command-line argument.</span></span>
* <span data-ttu-id="acc26-249">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="acc26-249">`urls` host configuration key.</span></span>
* <span data-ttu-id="acc26-250">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="acc26-250">`UseUrls` extension method.</span></span>

<span data-ttu-id="acc26-251">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="acc26-251">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="acc26-252">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="acc26-252">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="acc26-253">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="acc26-253">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="acc26-254">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="acc26-254">A development certificate is created:</span></span>

* <span data-ttu-id="acc26-255">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="acc26-255">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="acc26-256">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-256">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="acc26-257">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-257">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="acc26-258">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="acc26-258">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="acc26-259">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-259">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="acc26-260">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="acc26-260">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="acc26-261">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-261">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="acc26-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="acc26-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="acc26-263">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-263">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="acc26-264">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-264">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acc26-265">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="acc26-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="acc26-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="acc26-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="acc26-267">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-267">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="acc26-268">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-268">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acc26-269">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="acc26-269">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="acc26-270">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="acc26-270">Configure(IConfiguration)</span></span>

<span data-ttu-id="acc26-271">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-271">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="acc26-272">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="acc26-272">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="acc26-273">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="acc26-273">ListenOptions.UseHttps</span></span>

<span data-ttu-id="acc26-274">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-274">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="acc26-275">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="acc26-275">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="acc26-276">`UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-276">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="acc26-277">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="acc26-277">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="acc26-278">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="acc26-278">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="acc26-279">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="acc26-279">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="acc26-280">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="acc26-280">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="acc26-281">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-281">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="acc26-282">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="acc26-282">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="acc26-283">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="acc26-283">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="acc26-284">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="acc26-284">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="acc26-285">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-285">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="acc26-286">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="acc26-286">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="acc26-287">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-287">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="acc26-288">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-288">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="acc26-289">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-289">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="acc26-290">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-290">Supported configurations described next:</span></span>

* <span data-ttu-id="acc26-291">无配置</span><span class="sxs-lookup"><span data-stu-id="acc26-291">No configuration</span></span>
* <span data-ttu-id="acc26-292">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="acc26-292">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="acc26-293">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="acc26-293">Change the defaults in code</span></span>

<span data-ttu-id="acc26-294">*无配置*</span><span class="sxs-lookup"><span data-stu-id="acc26-294">*No configuration*</span></span>

<span data-ttu-id="acc26-295">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="acc26-295">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="acc26-296">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="acc26-296">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="acc26-297">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-297">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="acc26-298">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="acc26-298">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="acc26-299">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-299">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="acc26-300">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="acc26-300">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="acc26-301">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="acc26-301">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="acc26-302">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书  。</span><span class="sxs-lookup"><span data-stu-id="acc26-302">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="acc26-303">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书 。</span><span class="sxs-lookup"><span data-stu-id="acc26-303">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="acc26-304">例如，可将 Certificates > Default 证书指定为 ：</span><span class="sxs-lookup"><span data-stu-id="acc26-304">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="acc26-305">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="acc26-305">Schema notes:</span></span>

* <span data-ttu-id="acc26-306">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="acc26-306">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="acc26-307">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="acc26-307">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="acc26-308">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="acc26-308">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="acc26-309">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="acc26-309">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="acc26-310">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="acc26-310">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="acc26-311">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="acc26-311">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="acc26-312">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="acc26-312">The `Certificate` section is optional.</span></span> <span data-ttu-id="acc26-313">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="acc26-313">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="acc26-314">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="acc26-314">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="acc26-315">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书   。</span><span class="sxs-lookup"><span data-stu-id="acc26-315">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="acc26-316">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="acc26-316">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="acc26-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="acc26-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="acc26-318">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="acc26-318">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="acc26-319">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-319">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="acc26-320">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-320">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="acc26-321">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="acc26-321">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="acc26-322">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="acc26-322">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="acc26-323">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="acc26-323">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="acc26-324">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-324">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="acc26-325">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="acc26-325">*Change the defaults in code*</span></span>

<span data-ttu-id="acc26-326">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-326">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="acc26-327">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="acc26-327">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="acc26-328">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="acc26-328">*Kestrel support for SNI*</span></span>

<span data-ttu-id="acc26-329">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="acc26-329">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="acc26-330">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-330">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="acc26-331">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="acc26-331">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="acc26-332">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="acc26-332">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="acc26-333">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-333">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="acc26-334">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="acc26-334">SNI support requires:</span></span>

* <span data-ttu-id="acc26-335">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="acc26-335">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="acc26-336">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="acc26-336">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="acc26-337">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="acc26-337">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="acc26-338">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="acc26-338">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="acc26-339">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-339">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="acc26-340">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="acc26-340">Connection logging</span></span>

<span data-ttu-id="acc26-341">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="acc26-341">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="acc26-342">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="acc26-342">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="acc26-343">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="acc26-343">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="acc26-344">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="acc26-344">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="acc26-345">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="acc26-345">Bind to a TCP socket</span></span>

<span data-ttu-id="acc26-346"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-346">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="acc26-347">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-347">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="acc26-348">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-348">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="acc26-349">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="acc26-349">Bind to a Unix socket</span></span>

<span data-ttu-id="acc26-350">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="acc26-350">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="acc26-351">在 Nginx 配置文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="acc26-351">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="acc26-352">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="acc26-352">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="acc26-353">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="acc26-353">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="acc26-354">端口 0</span><span class="sxs-lookup"><span data-stu-id="acc26-354">Port 0</span></span>

<span data-ttu-id="acc26-355">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-355">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="acc26-356">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="acc26-356">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="acc26-357">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="acc26-357">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="acc26-358">限制</span><span class="sxs-lookup"><span data-stu-id="acc26-358">Limitations</span></span>

<span data-ttu-id="acc26-359">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="acc26-359">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="acc26-360">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="acc26-360">`--urls` command-line argument</span></span>
* <span data-ttu-id="acc26-361">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="acc26-361">`urls` host configuration key</span></span>
* <span data-ttu-id="acc26-362">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="acc26-362">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="acc26-363">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="acc26-363">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="acc26-364">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="acc26-364">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="acc26-365">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="acc26-365">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="acc26-366">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="acc26-366">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="acc26-367">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="acc26-367">IIS endpoint configuration</span></span>

<span data-ttu-id="acc26-368">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="acc26-368">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="acc26-369">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="acc26-369">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="acc26-370">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="acc26-370">ListenOptions.Protocols</span></span>

<span data-ttu-id="acc26-371">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="acc26-371">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="acc26-372">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="acc26-372">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="acc26-373">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="acc26-373">`HttpProtocols` enum value</span></span> | <span data-ttu-id="acc26-374">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="acc26-374">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="acc26-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-376">'Blazor'</span></span>
- <span data-ttu-id="acc26-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-377">'Identity'</span></span>
- <span data-ttu-id="acc26-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-379">'Razor'</span></span>
- <span data-ttu-id="acc26-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-382">'Blazor'</span></span>
- <span data-ttu-id="acc26-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-383">'Identity'</span></span>
- <span data-ttu-id="acc26-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-385">'Razor'</span></span>
- <span data-ttu-id="acc26-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-388">'Blazor'</span></span>
- <span data-ttu-id="acc26-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-389">'Identity'</span></span>
- <span data-ttu-id="acc26-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-391">'Razor'</span></span>
- <span data-ttu-id="acc26-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-394">'Blazor'</span></span>
- <span data-ttu-id="acc26-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-395">'Identity'</span></span>
- <span data-ttu-id="acc26-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-397">'Razor'</span></span>
- <span data-ttu-id="acc26-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-400">'Blazor'</span></span>
- <span data-ttu-id="acc26-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-401">'Identity'</span></span>
- <span data-ttu-id="acc26-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-403">'Razor'</span></span>
- <span data-ttu-id="acc26-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-406">'Blazor'</span></span>
- <span data-ttu-id="acc26-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-407">'Identity'</span></span>
- <span data-ttu-id="acc26-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-409">'Razor'</span></span>
- <span data-ttu-id="acc26-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-412">'Blazor'</span></span>
- <span data-ttu-id="acc26-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-413">'Identity'</span></span>
- <span data-ttu-id="acc26-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-415">'Razor'</span></span>
- <span data-ttu-id="acc26-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-418">'Blazor'</span></span>
- <span data-ttu-id="acc26-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-419">'Identity'</span></span>
- <span data-ttu-id="acc26-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-421">'Razor'</span></span>
- <span data-ttu-id="acc26-422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-424">'Blazor'</span></span>
- <span data-ttu-id="acc26-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-425">'Identity'</span></span>
- <span data-ttu-id="acc26-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-427">'Razor'</span></span>
- <span data-ttu-id="acc26-428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-430">'Blazor'</span></span>
- <span data-ttu-id="acc26-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-431">'Identity'</span></span>
- <span data-ttu-id="acc26-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-433">'Razor'</span></span>
- <span data-ttu-id="acc26-434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-436">'Blazor'</span></span>
- <span data-ttu-id="acc26-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-437">'Identity'</span></span>
- <span data-ttu-id="acc26-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-439">'Razor'</span></span>
- <span data-ttu-id="acc26-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-440">'SignalR' uid:</span></span> 

<span data-ttu-id="acc26-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-442">'Blazor'</span></span>
- <span data-ttu-id="acc26-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-443">'Identity'</span></span>
- <span data-ttu-id="acc26-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-445">'Razor'</span></span>
- <span data-ttu-id="acc26-446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-448">'Blazor'</span></span>
- <span data-ttu-id="acc26-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-449">'Identity'</span></span>
- <span data-ttu-id="acc26-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-451">'Razor'</span></span>
- <span data-ttu-id="acc26-452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-454">'Blazor'</span></span>
- <span data-ttu-id="acc26-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-455">'Identity'</span></span>
- <span data-ttu-id="acc26-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-457">'Razor'</span></span>
- <span data-ttu-id="acc26-458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-460">'Blazor'</span></span>
- <span data-ttu-id="acc26-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-461">'Identity'</span></span>
- <span data-ttu-id="acc26-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-463">'Razor'</span></span>
- <span data-ttu-id="acc26-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-466">'Blazor'</span></span>
- <span data-ttu-id="acc26-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-467">'Identity'</span></span>
- <span data-ttu-id="acc26-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-469">'Razor'</span></span>
- <span data-ttu-id="acc26-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-472">'Blazor'</span></span>
- <span data-ttu-id="acc26-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-473">'Identity'</span></span>
- <span data-ttu-id="acc26-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-475">'Razor'</span></span>
- <span data-ttu-id="acc26-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-478">'Blazor'</span></span>
- <span data-ttu-id="acc26-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-479">'Identity'</span></span>
- <span data-ttu-id="acc26-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-481">'Razor'</span></span>
- <span data-ttu-id="acc26-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-484">'Blazor'</span></span>
- <span data-ttu-id="acc26-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-485">'Identity'</span></span>
- <span data-ttu-id="acc26-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-487">'Razor'</span></span>
- <span data-ttu-id="acc26-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-490">'Blazor'</span></span>
- <span data-ttu-id="acc26-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-491">'Identity'</span></span>
- <span data-ttu-id="acc26-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-493">'Razor'</span></span>
- <span data-ttu-id="acc26-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-496">'Blazor'</span></span>
- <span data-ttu-id="acc26-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-497">'Identity'</span></span>
- <span data-ttu-id="acc26-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-499">'Razor'</span></span>
- <span data-ttu-id="acc26-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-502">'Blazor'</span></span>
- <span data-ttu-id="acc26-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-503">'Identity'</span></span>
- <span data-ttu-id="acc26-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-505">'Razor'</span></span>
- <span data-ttu-id="acc26-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-508">'Blazor'</span></span>
- <span data-ttu-id="acc26-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-509">'Identity'</span></span>
- <span data-ttu-id="acc26-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-511">'Razor'</span></span>
- <span data-ttu-id="acc26-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-512">'SignalR' uid:</span></span> 

<span data-ttu-id="acc26-513">--------------- | | `Http1`                    | 仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="acc26-513">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="acc26-514">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="acc26-514">Can be used with or without TLS.</span></span> <span data-ttu-id="acc26-515">| | `Http2`                    | 仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acc26-515">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="acc26-516">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="acc26-516">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="acc26-517">| | `Http1AndHttp2`            | HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acc26-517">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="acc26-518">HTTP/2 要求客户端在 TLS [应用层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手过程中选择 HTTP/2；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="acc26-518">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="acc26-519">任何终结点的默认 `ListenOptions.Protocols` 值都为 `HttpProtocols.Http1AndHttp2`。</span><span class="sxs-lookup"><span data-stu-id="acc26-519">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="acc26-520">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="acc26-520">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="acc26-521">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="acc26-521">TLS version 1.2 or later</span></span>
* <span data-ttu-id="acc26-522">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="acc26-522">Renegotiation disabled</span></span>
* <span data-ttu-id="acc26-523">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="acc26-523">Compression disabled</span></span>
* <span data-ttu-id="acc26-524">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="acc26-524">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="acc26-525">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;：最小 224 位</span><span class="sxs-lookup"><span data-stu-id="acc26-525">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="acc26-526">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;：最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="acc26-526">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="acc26-527">密码套件未列入阻止列表</span><span class="sxs-lookup"><span data-stu-id="acc26-527">Cipher suite not blacklisted</span></span>

<span data-ttu-id="acc26-528">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="acc26-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="acc26-529">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="acc26-529">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="acc26-530">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="acc26-530">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="acc26-531">使用连接中间件，针对特定密码的每个连接筛选 TLS 握手（如有必要）。</span><span class="sxs-lookup"><span data-stu-id="acc26-531">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="acc26-532">下面的示例针对应用不支持的任何密码算法引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="acc26-532">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="acc26-533">或者，定义 [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 并将其与可接受的密码套件列表进行比较。</span><span class="sxs-lookup"><span data-stu-id="acc26-533">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="acc26-534">没有哪种加密是使用 [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 密码算法。</span><span class="sxs-lookup"><span data-stu-id="acc26-534">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="acc26-535">连接筛选也可以通过 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda 进行配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-535">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="acc26-536">在 Linux 上，<xref:System.Net.Security.CipherSuitesPolicy> 可用于针对每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="acc26-536">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="acc26-537">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="acc26-537">*Set the protocol from configuration*</span></span>

<span data-ttu-id="acc26-538">`CreateDefaultBuilder` 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-538">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="acc26-539">以下 appsettings.json 示例将 HTTP/1.1 建立为所有终结点的默认连接协议：</span><span class="sxs-lookup"><span data-stu-id="acc26-539">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="acc26-540">以下 appsettings.json 示例将 HTTP/1.1 建立为所有指定终结点的连接协议：</span><span class="sxs-lookup"><span data-stu-id="acc26-540">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="acc26-541">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="acc26-541">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="acc26-542">传输配置</span><span class="sxs-lookup"><span data-stu-id="acc26-542">Transport configuration</span></span>

<span data-ttu-id="acc26-543">对于需要使用 Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) 的项目：</span><span class="sxs-lookup"><span data-stu-id="acc26-543">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="acc26-544">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="acc26-544">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="acc26-545">调用 `IWebHostBuilder` 上的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="acc26-545">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="acc26-546">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="acc26-546">URL prefixes</span></span>

<span data-ttu-id="acc26-547">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="acc26-547">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="acc26-548">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="acc26-548">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="acc26-549">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-549">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="acc26-550">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="acc26-550">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="acc26-551">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="acc26-551">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="acc26-552">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="acc26-552">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="acc26-553">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="acc26-553">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="acc26-554">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="acc26-554">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="acc26-555">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="acc26-555">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="acc26-556">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="acc26-556">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="acc26-557">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="acc26-557">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="acc26-558">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acc26-558">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="acc26-559">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="acc26-559">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="acc26-560">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="acc26-560">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="acc26-561">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="acc26-561">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="acc26-562">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="acc26-562">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="acc26-563">主机筛选</span><span class="sxs-lookup"><span data-stu-id="acc26-563">Host filtering</span></span>

<span data-ttu-id="acc26-564">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="acc26-564">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="acc26-565">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="acc26-565">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="acc26-566">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="acc26-566">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="acc26-567">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="acc26-567">`Host` headers aren't validated.</span></span>

<span data-ttu-id="acc26-568">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="acc26-568">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="acc26-569">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包为 ASP.NET Core 应用隐式提供。</span><span class="sxs-lookup"><span data-stu-id="acc26-569">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="acc26-570">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="acc26-570">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="acc26-571">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="acc26-571">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="acc26-572">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="acc26-572">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="acc26-573">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="acc26-573">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="acc26-574">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="acc26-574">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="acc26-575">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="acc26-575">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="acc26-576">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="acc26-576">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="acc26-577">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="acc26-577">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="acc26-578">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="acc26-578">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="acc26-579">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="acc26-579">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="acc26-580">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="acc26-580">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="acc26-581">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="acc26-581">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="acc26-582">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="acc26-582">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="acc26-583">HTTPS</span><span class="sxs-lookup"><span data-stu-id="acc26-583">HTTPS</span></span>
* <span data-ttu-id="acc26-584">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="acc26-584">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="acc26-585">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="acc26-585">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="acc26-586">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="acc26-586">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="acc26-587">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acc26-587">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="acc26-588">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-588">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="acc26-589">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="acc26-589">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="acc26-590">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="acc26-590">HTTP/2 support</span></span>

<span data-ttu-id="acc26-591">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="acc26-591">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="acc26-592">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="acc26-592">Operating system&dagger;</span></span>
  * <span data-ttu-id="acc26-593">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="acc26-593">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="acc26-594">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="acc26-594">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="acc26-595">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="acc26-595">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="acc26-596">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="acc26-596">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="acc26-597">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="acc26-597">TLS 1.2 or later connection</span></span>

<span data-ttu-id="acc26-598">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acc26-598">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="acc26-599">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="acc26-599">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="acc26-600">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="acc26-600">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="acc26-601">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="acc26-601">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="acc26-602">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="acc26-602">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="acc26-603">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acc26-603">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="acc26-604">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="acc26-604">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="acc26-605">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="acc26-605">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="acc26-606">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="acc26-606">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="acc26-607">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-607">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="acc26-608">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="acc26-608">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="acc26-610">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-610">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="acc26-612">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-612">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="acc26-613">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-613">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="acc26-614">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="acc26-614">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="acc26-615">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-615">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="acc26-616">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="acc26-616">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="acc26-617">反向代理：</span><span class="sxs-lookup"><span data-stu-id="acc26-617">A reverse proxy:</span></span>

* <span data-ttu-id="acc26-618">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="acc26-618">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="acc26-619">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="acc26-619">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="acc26-620">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="acc26-620">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="acc26-621">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-621">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="acc26-622">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="acc26-622">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="acc26-623">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acc26-623">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="acc26-624">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="acc26-624">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="acc26-625">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="acc26-625">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="acc26-626">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-626">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="acc26-627">在 Program.cs 中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="acc26-627">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="acc26-628">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”部分。</span><span class="sxs-lookup"><span data-stu-id="acc26-628">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="acc26-629">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="acc26-629">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="acc26-630">如果应用未调用 `CreateDefaultBuilder` 来设置主机，请在调用 `ConfigureKestrel` 之前先调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="acc26-630">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="acc26-631">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="acc26-631">Kestrel options</span></span>

<span data-ttu-id="acc26-632">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="acc26-632">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="acc26-633">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="acc26-633">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="acc26-634">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="acc26-634">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="acc26-635">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="acc26-635">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="acc26-636">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-636">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="acc26-637">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置 ：</span><span class="sxs-lookup"><span data-stu-id="acc26-637">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="acc26-638">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="acc26-638">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="acc26-639">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="acc26-639">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="acc26-640">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="acc26-640">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="acc26-641">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="acc26-641">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="acc26-642">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="acc26-642">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="acc26-643">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="acc26-643">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="acc26-644">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="acc26-644">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="acc26-645">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="acc26-645">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="acc26-646">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="acc26-646">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="acc26-647">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="acc26-647">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="acc26-648">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="acc26-648">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="acc26-649">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="acc26-649">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="acc26-650">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="acc26-650">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="acc26-651">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-651">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="acc26-652">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-652">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="acc26-653">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="acc26-653">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="acc26-654">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="acc26-654">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="acc26-655">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="acc26-655">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="acc26-656">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="acc26-656">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="acc26-657">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="acc26-657">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="acc26-658">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="acc26-658">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="acc26-659">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="acc26-659">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="acc26-660">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-660">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="acc26-661">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-661">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="acc26-662">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="acc26-662">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="acc26-663">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="acc26-663">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="acc26-664">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="acc26-664">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="acc26-665">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="acc26-665">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="acc26-666">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="acc26-666">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="acc26-667">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-667">A minimum rate also applies to the response.</span></span> <span data-ttu-id="acc26-668">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="acc26-668">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="acc26-669">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="acc26-669">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="acc26-670">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="acc26-670">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="acc26-671">由于协议支持请求多路复用，HTTP/2 不支持基于每个请求修改速率限制，因此 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的速率特性。</span><span class="sxs-lookup"><span data-stu-id="acc26-671">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="acc26-672">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="acc26-672">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="acc26-673">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="acc26-673">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="acc26-674">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="acc26-674">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="acc26-675">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="acc26-675">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="acc26-676">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="acc26-676">Maximum streams per connection</span></span>

<span data-ttu-id="acc26-677">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="acc26-677">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="acc26-678">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="acc26-678">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="acc26-679">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="acc26-679">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="acc26-680">标题表大小</span><span class="sxs-lookup"><span data-stu-id="acc26-680">Header table size</span></span>

<span data-ttu-id="acc26-681">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="acc26-681">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="acc26-682">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="acc26-682">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="acc26-683">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="acc26-683">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="acc26-684">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="acc26-684">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="acc26-685">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="acc26-685">Maximum frame size</span></span>

<span data-ttu-id="acc26-686">`Http2.MaxFrameSize` 指示要接收的 HTTP/2 连接帧有效负载的最大大小。</span><span class="sxs-lookup"><span data-stu-id="acc26-686">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="acc26-687">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="acc26-687">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="acc26-688">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="acc26-688">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="acc26-689">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="acc26-689">Maximum request header size</span></span>

<span data-ttu-id="acc26-690">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="acc26-690">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="acc26-691">此限制同时适用于压缩和未压缩表示形式中的名称和值。</span><span class="sxs-lookup"><span data-stu-id="acc26-691">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="acc26-692">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="acc26-692">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="acc26-693">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="acc26-693">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="acc26-694">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="acc26-694">Initial connection window size</span></span>

<span data-ttu-id="acc26-695">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="acc26-695">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="acc26-696">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-696">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="acc26-697">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="acc26-697">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="acc26-698">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="acc26-698">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="acc26-699">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="acc26-699">Initial stream window size</span></span>

<span data-ttu-id="acc26-700">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="acc26-700">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="acc26-701">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-701">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="acc26-702">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="acc26-702">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="acc26-703">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="acc26-703">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="acc26-704">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="acc26-704">Synchronous I/O</span></span>

<span data-ttu-id="acc26-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="acc26-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="acc26-706">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="acc26-706">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="acc26-707">大量阻止同步 I/O 的操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-707">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="acc26-708">仅在使用不支持异步 I/O 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="acc26-708">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="acc26-709">以下示例会启用同步 I/O：</span><span class="sxs-lookup"><span data-stu-id="acc26-709">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="acc26-710">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="acc26-710">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="acc26-711">终结点配置</span><span class="sxs-lookup"><span data-stu-id="acc26-711">Endpoint configuration</span></span>

<span data-ttu-id="acc26-712">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="acc26-712">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="acc26-713">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="acc26-713">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="acc26-714">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="acc26-714">Specify URLs using the:</span></span>

* <span data-ttu-id="acc26-715">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="acc26-715">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="acc26-716">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="acc26-716">`--urls` command-line argument.</span></span>
* <span data-ttu-id="acc26-717">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="acc26-717">`urls` host configuration key.</span></span>
* <span data-ttu-id="acc26-718">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="acc26-718">`UseUrls` extension method.</span></span>

<span data-ttu-id="acc26-719">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="acc26-719">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="acc26-720">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="acc26-720">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="acc26-721">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="acc26-721">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="acc26-722">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="acc26-722">A development certificate is created:</span></span>

* <span data-ttu-id="acc26-723">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="acc26-723">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="acc26-724">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-724">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="acc26-725">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-725">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="acc26-726">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="acc26-726">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="acc26-727">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-727">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="acc26-728">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="acc26-728">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="acc26-729">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-729">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="acc26-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="acc26-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="acc26-731">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-731">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="acc26-732">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-732">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acc26-733">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="acc26-733">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="acc26-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="acc26-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="acc26-735">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-735">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="acc26-736">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-736">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acc26-737">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="acc26-737">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="acc26-738">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="acc26-738">Configure(IConfiguration)</span></span>

<span data-ttu-id="acc26-739">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-739">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="acc26-740">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="acc26-740">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="acc26-741">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="acc26-741">ListenOptions.UseHttps</span></span>

<span data-ttu-id="acc26-742">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-742">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="acc26-743">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="acc26-743">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="acc26-744">`UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-744">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="acc26-745">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="acc26-745">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="acc26-746">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="acc26-746">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="acc26-747">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="acc26-747">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="acc26-748">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="acc26-748">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="acc26-749">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-749">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="acc26-750">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="acc26-750">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="acc26-751">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="acc26-751">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="acc26-752">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="acc26-752">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="acc26-753">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-753">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="acc26-754">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="acc26-754">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="acc26-755">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-755">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="acc26-756">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-756">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="acc26-757">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-757">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="acc26-758">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-758">Supported configurations described next:</span></span>

* <span data-ttu-id="acc26-759">无配置</span><span class="sxs-lookup"><span data-stu-id="acc26-759">No configuration</span></span>
* <span data-ttu-id="acc26-760">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="acc26-760">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="acc26-761">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="acc26-761">Change the defaults in code</span></span>

<span data-ttu-id="acc26-762">*无配置*</span><span class="sxs-lookup"><span data-stu-id="acc26-762">*No configuration*</span></span>

<span data-ttu-id="acc26-763">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="acc26-763">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="acc26-764">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="acc26-764">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="acc26-765">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-765">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="acc26-766">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="acc26-766">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="acc26-767">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-767">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="acc26-768">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="acc26-768">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="acc26-769">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="acc26-769">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="acc26-770">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书  。</span><span class="sxs-lookup"><span data-stu-id="acc26-770">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="acc26-771">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书 。</span><span class="sxs-lookup"><span data-stu-id="acc26-771">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="acc26-772">例如，可将 Certificates > Default 证书指定为 ：</span><span class="sxs-lookup"><span data-stu-id="acc26-772">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="acc26-773">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="acc26-773">Schema notes:</span></span>

* <span data-ttu-id="acc26-774">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="acc26-774">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="acc26-775">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="acc26-775">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="acc26-776">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="acc26-776">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="acc26-777">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="acc26-777">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="acc26-778">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="acc26-778">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="acc26-779">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="acc26-779">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="acc26-780">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="acc26-780">The `Certificate` section is optional.</span></span> <span data-ttu-id="acc26-781">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="acc26-781">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="acc26-782">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="acc26-782">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="acc26-783">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书   。</span><span class="sxs-lookup"><span data-stu-id="acc26-783">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="acc26-784">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="acc26-784">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="acc26-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="acc26-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="acc26-786">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="acc26-786">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="acc26-787">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-787">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="acc26-788">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-788">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="acc26-789">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="acc26-789">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="acc26-790">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="acc26-790">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="acc26-791">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="acc26-791">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="acc26-792">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-792">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="acc26-793">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="acc26-793">*Change the defaults in code*</span></span>

<span data-ttu-id="acc26-794">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-794">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="acc26-795">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="acc26-795">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="acc26-796">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="acc26-796">*Kestrel support for SNI*</span></span>

<span data-ttu-id="acc26-797">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="acc26-797">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="acc26-798">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-798">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="acc26-799">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="acc26-799">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="acc26-800">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="acc26-800">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="acc26-801">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-801">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="acc26-802">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="acc26-802">SNI support requires:</span></span>

* <span data-ttu-id="acc26-803">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="acc26-803">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="acc26-804">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="acc26-804">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="acc26-805">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="acc26-805">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="acc26-806">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="acc26-806">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="acc26-807">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-807">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="acc26-808">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="acc26-808">Connection logging</span></span>

<span data-ttu-id="acc26-809">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="acc26-809">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="acc26-810">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="acc26-810">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="acc26-811">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="acc26-811">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="acc26-812">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="acc26-812">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="acc26-813">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="acc26-813">Bind to a TCP socket</span></span>

<span data-ttu-id="acc26-814"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-814">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="acc26-815">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-815">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="acc26-816">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-816">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="acc26-817">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="acc26-817">Bind to a Unix socket</span></span>

<span data-ttu-id="acc26-818">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="acc26-818">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="acc26-819">在 Nginx confiuguration 文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="acc26-819">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="acc26-820">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="acc26-820">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="acc26-821">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="acc26-821">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="acc26-822">端口 0</span><span class="sxs-lookup"><span data-stu-id="acc26-822">Port 0</span></span>

<span data-ttu-id="acc26-823">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-823">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="acc26-824">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="acc26-824">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="acc26-825">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="acc26-825">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="acc26-826">限制</span><span class="sxs-lookup"><span data-stu-id="acc26-826">Limitations</span></span>

<span data-ttu-id="acc26-827">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="acc26-827">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="acc26-828">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="acc26-828">`--urls` command-line argument</span></span>
* <span data-ttu-id="acc26-829">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="acc26-829">`urls` host configuration key</span></span>
* <span data-ttu-id="acc26-830">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="acc26-830">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="acc26-831">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="acc26-831">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="acc26-832">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="acc26-832">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="acc26-833">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="acc26-833">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="acc26-834">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="acc26-834">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="acc26-835">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="acc26-835">IIS endpoint configuration</span></span>

<span data-ttu-id="acc26-836">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="acc26-836">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="acc26-837">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="acc26-837">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="acc26-838">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="acc26-838">ListenOptions.Protocols</span></span>

<span data-ttu-id="acc26-839">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="acc26-839">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="acc26-840">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="acc26-840">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="acc26-841">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="acc26-841">`HttpProtocols` enum value</span></span> | <span data-ttu-id="acc26-842">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="acc26-842">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="acc26-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-844">'Blazor'</span></span>
- <span data-ttu-id="acc26-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-845">'Identity'</span></span>
- <span data-ttu-id="acc26-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-847">'Razor'</span></span>
- <span data-ttu-id="acc26-848">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-850">'Blazor'</span></span>
- <span data-ttu-id="acc26-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-851">'Identity'</span></span>
- <span data-ttu-id="acc26-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-853">'Razor'</span></span>
- <span data-ttu-id="acc26-854">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-856">'Blazor'</span></span>
- <span data-ttu-id="acc26-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-857">'Identity'</span></span>
- <span data-ttu-id="acc26-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-859">'Razor'</span></span>
- <span data-ttu-id="acc26-860">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-862">'Blazor'</span></span>
- <span data-ttu-id="acc26-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-863">'Identity'</span></span>
- <span data-ttu-id="acc26-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-865">'Razor'</span></span>
- <span data-ttu-id="acc26-866">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-868">'Blazor'</span></span>
- <span data-ttu-id="acc26-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-869">'Identity'</span></span>
- <span data-ttu-id="acc26-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-871">'Razor'</span></span>
- <span data-ttu-id="acc26-872">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-874">'Blazor'</span></span>
- <span data-ttu-id="acc26-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-875">'Identity'</span></span>
- <span data-ttu-id="acc26-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-877">'Razor'</span></span>
- <span data-ttu-id="acc26-878">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-880">'Blazor'</span></span>
- <span data-ttu-id="acc26-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-881">'Identity'</span></span>
- <span data-ttu-id="acc26-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-883">'Razor'</span></span>
- <span data-ttu-id="acc26-884">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-886">'Blazor'</span></span>
- <span data-ttu-id="acc26-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-887">'Identity'</span></span>
- <span data-ttu-id="acc26-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-889">'Razor'</span></span>
- <span data-ttu-id="acc26-890">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-892">'Blazor'</span></span>
- <span data-ttu-id="acc26-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-893">'Identity'</span></span>
- <span data-ttu-id="acc26-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-895">'Razor'</span></span>
- <span data-ttu-id="acc26-896">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-896">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-898">'Blazor'</span></span>
- <span data-ttu-id="acc26-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-899">'Identity'</span></span>
- <span data-ttu-id="acc26-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-901">'Razor'</span></span>
- <span data-ttu-id="acc26-902">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-902">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-904">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-904">'Blazor'</span></span>
- <span data-ttu-id="acc26-905">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-905">'Identity'</span></span>
- <span data-ttu-id="acc26-906">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-906">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-907">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-907">'Razor'</span></span>
- <span data-ttu-id="acc26-908">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-908">'SignalR' uid:</span></span> 

<span data-ttu-id="acc26-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-910">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-910">'Blazor'</span></span>
- <span data-ttu-id="acc26-911">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-911">'Identity'</span></span>
- <span data-ttu-id="acc26-912">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-912">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-913">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-913">'Razor'</span></span>
- <span data-ttu-id="acc26-914">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-914">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-916">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-916">'Blazor'</span></span>
- <span data-ttu-id="acc26-917">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-917">'Identity'</span></span>
- <span data-ttu-id="acc26-918">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-918">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-919">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-919">'Razor'</span></span>
- <span data-ttu-id="acc26-920">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-920">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-922">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-922">'Blazor'</span></span>
- <span data-ttu-id="acc26-923">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-923">'Identity'</span></span>
- <span data-ttu-id="acc26-924">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-924">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-925">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-925">'Razor'</span></span>
- <span data-ttu-id="acc26-926">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-926">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-928">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-928">'Blazor'</span></span>
- <span data-ttu-id="acc26-929">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-929">'Identity'</span></span>
- <span data-ttu-id="acc26-930">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-930">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-931">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-931">'Razor'</span></span>
- <span data-ttu-id="acc26-932">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-932">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-934">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-934">'Blazor'</span></span>
- <span data-ttu-id="acc26-935">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-935">'Identity'</span></span>
- <span data-ttu-id="acc26-936">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-936">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-937">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-937">'Razor'</span></span>
- <span data-ttu-id="acc26-938">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-938">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-940">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-940">'Blazor'</span></span>
- <span data-ttu-id="acc26-941">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-941">'Identity'</span></span>
- <span data-ttu-id="acc26-942">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-942">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-943">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-943">'Razor'</span></span>
- <span data-ttu-id="acc26-944">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-944">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-946">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-946">'Blazor'</span></span>
- <span data-ttu-id="acc26-947">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-947">'Identity'</span></span>
- <span data-ttu-id="acc26-948">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-948">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-949">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-949">'Razor'</span></span>
- <span data-ttu-id="acc26-950">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-950">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-952">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-952">'Blazor'</span></span>
- <span data-ttu-id="acc26-953">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-953">'Identity'</span></span>
- <span data-ttu-id="acc26-954">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-954">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-955">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-955">'Razor'</span></span>
- <span data-ttu-id="acc26-956">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-956">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-958">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-958">'Blazor'</span></span>
- <span data-ttu-id="acc26-959">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-959">'Identity'</span></span>
- <span data-ttu-id="acc26-960">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-960">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-961">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-961">'Razor'</span></span>
- <span data-ttu-id="acc26-962">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-962">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-964">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-964">'Blazor'</span></span>
- <span data-ttu-id="acc26-965">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-965">'Identity'</span></span>
- <span data-ttu-id="acc26-966">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-966">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-967">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-967">'Razor'</span></span>
- <span data-ttu-id="acc26-968">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-968">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-970">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-970">'Blazor'</span></span>
- <span data-ttu-id="acc26-971">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-971">'Identity'</span></span>
- <span data-ttu-id="acc26-972">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-972">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-973">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-973">'Razor'</span></span>
- <span data-ttu-id="acc26-974">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-974">'SignalR' uid:</span></span> 

-
<span data-ttu-id="acc26-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="acc26-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="acc26-976">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="acc26-976">'Blazor'</span></span>
- <span data-ttu-id="acc26-977">'Identity'</span><span class="sxs-lookup"><span data-stu-id="acc26-977">'Identity'</span></span>
- <span data-ttu-id="acc26-978">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="acc26-978">'Let's Encrypt'</span></span>
- <span data-ttu-id="acc26-979">'Razor'</span><span class="sxs-lookup"><span data-stu-id="acc26-979">'Razor'</span></span>
- <span data-ttu-id="acc26-980">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="acc26-980">'SignalR' uid:</span></span> 

<span data-ttu-id="acc26-981">--------------- | | `Http1`                    | 仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="acc26-981">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="acc26-982">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="acc26-982">Can be used with or without TLS.</span></span> <span data-ttu-id="acc26-983">| | `Http2`                    | 仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acc26-983">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="acc26-984">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="acc26-984">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="acc26-985">| | `Http1AndHttp2`            | HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="acc26-985">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="acc26-986">HTTP/2 需要 TLS 和[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="acc26-986">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="acc26-987">默认协议是 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="acc26-987">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="acc26-988">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="acc26-988">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="acc26-989">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="acc26-989">TLS version 1.2 or later</span></span>
* <span data-ttu-id="acc26-990">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="acc26-990">Renegotiation disabled</span></span>
* <span data-ttu-id="acc26-991">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="acc26-991">Compression disabled</span></span>
* <span data-ttu-id="acc26-992">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="acc26-992">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="acc26-993">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;：最小 224 位</span><span class="sxs-lookup"><span data-stu-id="acc26-993">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="acc26-994">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;：最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="acc26-994">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="acc26-995">密码套件未列入阻止列表</span><span class="sxs-lookup"><span data-stu-id="acc26-995">Cipher suite not blacklisted</span></span>

<span data-ttu-id="acc26-996">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="acc26-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="acc26-997">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="acc26-997">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="acc26-998">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="acc26-998">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="acc26-999">（可选）创建 `IConnectionAdapter` 实现，以针对特定密码的每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="acc26-999">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="acc26-1000">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="acc26-1000">*Set the protocol from configuration*</span></span>

<span data-ttu-id="acc26-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="acc26-1002">在以下 appsettings.json 示例中，为 Kestrel 的所有终结点建立默认的连接协议（HTTP/1.1 和 HTTP/2）：</span><span class="sxs-lookup"><span data-stu-id="acc26-1002">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="acc26-1003">以下配置文件示例为特定终结点建立了连接协议：</span><span class="sxs-lookup"><span data-stu-id="acc26-1003">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="acc26-1004">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="acc26-1004">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="acc26-1005">传输配置</span><span class="sxs-lookup"><span data-stu-id="acc26-1005">Transport configuration</span></span>

<span data-ttu-id="acc26-1006">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="acc26-1006">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="acc26-1007">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="acc26-1007">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="acc26-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="acc26-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="acc26-1009">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="acc26-1009">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="acc26-1010">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="acc26-1010">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="acc26-1011">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="acc26-1011">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="acc26-1012">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="acc26-1012">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="acc26-1013">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="acc26-1013">URL prefixes</span></span>

<span data-ttu-id="acc26-1014">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="acc26-1014">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="acc26-1015">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="acc26-1015">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="acc26-1016">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-1016">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="acc26-1017">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="acc26-1017">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="acc26-1018">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="acc26-1018">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="acc26-1019">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="acc26-1019">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="acc26-1020">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="acc26-1020">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="acc26-1021">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="acc26-1021">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="acc26-1022">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="acc26-1022">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="acc26-1023">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="acc26-1023">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="acc26-1024">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="acc26-1024">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="acc26-1025">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1025">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="acc26-1026">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="acc26-1026">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="acc26-1027">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="acc26-1027">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="acc26-1028">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="acc26-1028">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="acc26-1029">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="acc26-1029">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="acc26-1030">主机筛选</span><span class="sxs-lookup"><span data-stu-id="acc26-1030">Host filtering</span></span>

<span data-ttu-id="acc26-1031">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="acc26-1031">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="acc26-1032">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="acc26-1032">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="acc26-1033">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="acc26-1033">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="acc26-1034">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="acc26-1034">`Host` headers aren't validated.</span></span>

<span data-ttu-id="acc26-1035">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="acc26-1035">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="acc26-1036">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1036">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="acc26-1037">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="acc26-1037">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="acc26-1038">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="acc26-1038">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="acc26-1039">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="acc26-1039">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="acc26-1040">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="acc26-1040">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="acc26-1041">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="acc26-1041">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="acc26-1042">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="acc26-1042">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="acc26-1043">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="acc26-1043">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="acc26-1044">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="acc26-1044">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="acc26-1045">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="acc26-1045">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="acc26-1046">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="acc26-1046">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="acc26-1047">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1047">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="acc26-1048">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="acc26-1048">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="acc26-1049">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="acc26-1049">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="acc26-1050">HTTPS</span><span class="sxs-lookup"><span data-stu-id="acc26-1050">HTTPS</span></span>
* <span data-ttu-id="acc26-1051">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="acc26-1051">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="acc26-1052">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="acc26-1052">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="acc26-1053">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-1053">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="acc26-1054">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="acc26-1054">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="acc26-1055">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="acc26-1055">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="acc26-1056">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="acc26-1056">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="acc26-1057">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-1057">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="acc26-1058">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="acc26-1058">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="acc26-1060">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-1060">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="acc26-1062">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1062">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="acc26-1063">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-1063">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="acc26-1064">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1064">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="acc26-1065">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-1065">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="acc26-1066">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="acc26-1066">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="acc26-1067">反向代理：</span><span class="sxs-lookup"><span data-stu-id="acc26-1067">A reverse proxy:</span></span>

* <span data-ttu-id="acc26-1068">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="acc26-1068">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="acc26-1069">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="acc26-1069">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="acc26-1070">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="acc26-1070">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="acc26-1071">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1071">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="acc26-1072">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="acc26-1072">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="acc26-1073">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1073">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="acc26-1074">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="acc26-1074">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="acc26-1075">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="acc26-1075">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="acc26-1076">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-1076">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="acc26-1077">在 Program.cs 中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="acc26-1077">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="acc26-1078">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="acc26-1078">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="acc26-1079">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”部分。</span><span class="sxs-lookup"><span data-stu-id="acc26-1079">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="acc26-1080">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="acc26-1080">Kestrel options</span></span>

<span data-ttu-id="acc26-1081">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="acc26-1081">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="acc26-1082">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="acc26-1082">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="acc26-1083">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="acc26-1083">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="acc26-1084">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="acc26-1084">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="acc26-1085">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1085">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="acc26-1086">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置 ：</span><span class="sxs-lookup"><span data-stu-id="acc26-1086">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="acc26-1087">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="acc26-1087">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="acc26-1088">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="acc26-1088">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="acc26-1089">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="acc26-1089">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="acc26-1090">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="acc26-1090">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="acc26-1091">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="acc26-1091">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="acc26-1092">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="acc26-1092">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="acc26-1093">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="acc26-1093">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="acc26-1094">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1094">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="acc26-1095">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="acc26-1095">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="acc26-1096">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1096">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="acc26-1097">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="acc26-1097">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="acc26-1098">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="acc26-1098">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="acc26-1099">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="acc26-1099">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="acc26-1100">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-1100">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="acc26-1101">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-1101">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="acc26-1102">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1102">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="acc26-1103">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="acc26-1103">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="acc26-1104">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="acc26-1104">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="acc26-1105">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="acc26-1105">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="acc26-1106">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="acc26-1106">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="acc26-1107">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="acc26-1107">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="acc26-1108">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="acc26-1108">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="acc26-1109">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-1109">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="acc26-1110">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="acc26-1110">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="acc26-1111">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="acc26-1111">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="acc26-1112">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="acc26-1112">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="acc26-1113">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="acc26-1113">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="acc26-1114">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="acc26-1114">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="acc26-1115">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="acc26-1115">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="acc26-1116">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-1116">A minimum rate also applies to the response.</span></span> <span data-ttu-id="acc26-1117">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="acc26-1117">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="acc26-1118">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="acc26-1118">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="acc26-1119">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="acc26-1119">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="acc26-1120">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="acc26-1120">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="acc26-1121">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="acc26-1121">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="acc26-1122">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="acc26-1122">Synchronous I/O</span></span>

<span data-ttu-id="acc26-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="acc26-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="acc26-1124">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1124">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="acc26-1125">大量阻止同步 I/O 的操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-1125">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="acc26-1126">仅在使用不支持异步 I/O 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1126">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="acc26-1127">以下示例会禁用同步 I/O：</span><span class="sxs-lookup"><span data-stu-id="acc26-1127">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="acc26-1128">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="acc26-1128">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="acc26-1129">终结点配置</span><span class="sxs-lookup"><span data-stu-id="acc26-1129">Endpoint configuration</span></span>

<span data-ttu-id="acc26-1130">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="acc26-1130">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="acc26-1131">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="acc26-1131">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="acc26-1132">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="acc26-1132">Specify URLs using the:</span></span>

* <span data-ttu-id="acc26-1133">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="acc26-1133">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="acc26-1134">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="acc26-1134">`--urls` command-line argument.</span></span>
* <span data-ttu-id="acc26-1135">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="acc26-1135">`urls` host configuration key.</span></span>
* <span data-ttu-id="acc26-1136">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="acc26-1136">`UseUrls` extension method.</span></span>

<span data-ttu-id="acc26-1137">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1137">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="acc26-1138">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1138">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="acc26-1139">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1139">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="acc26-1140">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="acc26-1140">A development certificate is created:</span></span>

* <span data-ttu-id="acc26-1141">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="acc26-1141">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="acc26-1142">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-1142">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="acc26-1143">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-1143">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="acc26-1144">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1144">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="acc26-1145">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-1145">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="acc26-1146">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1146">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="acc26-1147">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-1147">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="acc26-1148">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="acc26-1148">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="acc26-1149">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1149">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="acc26-1150">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1150">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acc26-1151">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="acc26-1151">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="acc26-1152">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="acc26-1152">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="acc26-1153">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1153">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="acc26-1154">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1154">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="acc26-1155">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="acc26-1155">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="acc26-1156">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="acc26-1156">Configure(IConfiguration)</span></span>

<span data-ttu-id="acc26-1157">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="acc26-1157">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="acc26-1158">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="acc26-1158">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="acc26-1159">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="acc26-1159">ListenOptions.UseHttps</span></span>

<span data-ttu-id="acc26-1160">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-1160">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="acc26-1161">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="acc26-1161">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="acc26-1162">`UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-1162">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="acc26-1163">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="acc26-1163">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="acc26-1164">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="acc26-1164">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="acc26-1165">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="acc26-1165">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="acc26-1166">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="acc26-1166">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="acc26-1167">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1167">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="acc26-1168">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1168">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="acc26-1169">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="acc26-1169">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="acc26-1170">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="acc26-1170">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="acc26-1171">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-1171">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="acc26-1172">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1172">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="acc26-1173">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-1173">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="acc26-1174">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-1174">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="acc26-1175">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-1175">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="acc26-1176">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-1176">Supported configurations described next:</span></span>

* <span data-ttu-id="acc26-1177">无配置</span><span class="sxs-lookup"><span data-stu-id="acc26-1177">No configuration</span></span>
* <span data-ttu-id="acc26-1178">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="acc26-1178">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="acc26-1179">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="acc26-1179">Change the defaults in code</span></span>

<span data-ttu-id="acc26-1180">*无配置*</span><span class="sxs-lookup"><span data-stu-id="acc26-1180">*No configuration*</span></span>

<span data-ttu-id="acc26-1181">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1181">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="acc26-1182">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="acc26-1182">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="acc26-1183">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1183">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="acc26-1184">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="acc26-1184">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="acc26-1185">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-1185">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="acc26-1186">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="acc26-1186">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="acc26-1187">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1187">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="acc26-1188">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书  。</span><span class="sxs-lookup"><span data-stu-id="acc26-1188">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="acc26-1189">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书 。</span><span class="sxs-lookup"><span data-stu-id="acc26-1189">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="acc26-1190">例如，可将 Certificates > Default 证书指定为 ：</span><span class="sxs-lookup"><span data-stu-id="acc26-1190">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="acc26-1191">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="acc26-1191">Schema notes:</span></span>

* <span data-ttu-id="acc26-1192">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="acc26-1192">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="acc26-1193">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="acc26-1193">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="acc26-1194">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="acc26-1194">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="acc26-1195">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="acc26-1195">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="acc26-1196">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="acc26-1196">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="acc26-1197">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="acc26-1197">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="acc26-1198">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="acc26-1198">The `Certificate` section is optional.</span></span> <span data-ttu-id="acc26-1199">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="acc26-1199">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="acc26-1200">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="acc26-1200">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="acc26-1201">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书   。</span><span class="sxs-lookup"><span data-stu-id="acc26-1201">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="acc26-1202">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="acc26-1202">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="acc26-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="acc26-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="acc26-1204">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="acc26-1204">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="acc26-1205">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1205">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="acc26-1206">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1206">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="acc26-1207">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1207">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="acc26-1208">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="acc26-1208">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="acc26-1209">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="acc26-1209">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="acc26-1210">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1210">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="acc26-1211">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="acc26-1211">*Change the defaults in code*</span></span>

<span data-ttu-id="acc26-1212">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-1212">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="acc26-1213">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1213">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="acc26-1214">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="acc26-1214">*Kestrel support for SNI*</span></span>

<span data-ttu-id="acc26-1215">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="acc26-1215">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="acc26-1216">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-1216">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="acc26-1217">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="acc26-1217">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="acc26-1218">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="acc26-1218">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="acc26-1219">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="acc26-1219">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="acc26-1220">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="acc26-1220">SNI support requires:</span></span>

* <span data-ttu-id="acc26-1221">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="acc26-1221">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="acc26-1222">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1222">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="acc26-1223">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1223">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="acc26-1224">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="acc26-1224">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="acc26-1225">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-1225">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="acc26-1226">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="acc26-1226">Connection logging</span></span>

<span data-ttu-id="acc26-1227">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="acc26-1227">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="acc26-1228">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="acc26-1228">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="acc26-1229">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="acc26-1229">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="acc26-1230">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="acc26-1230">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="acc26-1231">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="acc26-1231">Bind to a TCP socket</span></span>

<span data-ttu-id="acc26-1232"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="acc26-1232">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="acc26-1233">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-1233">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="acc26-1234">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1234">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="acc26-1235">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="acc26-1235">Bind to a Unix socket</span></span>

<span data-ttu-id="acc26-1236">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="acc26-1236">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="acc26-1237">在 Nginx confiuguration 文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1237">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="acc26-1238">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1238">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="acc26-1239">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="acc26-1239">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="acc26-1240">端口 0</span><span class="sxs-lookup"><span data-stu-id="acc26-1240">Port 0</span></span>

<span data-ttu-id="acc26-1241">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="acc26-1241">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="acc26-1242">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="acc26-1242">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="acc26-1243">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="acc26-1243">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="acc26-1244">限制</span><span class="sxs-lookup"><span data-stu-id="acc26-1244">Limitations</span></span>

<span data-ttu-id="acc26-1245">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="acc26-1245">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="acc26-1246">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="acc26-1246">`--urls` command-line argument</span></span>
* <span data-ttu-id="acc26-1247">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="acc26-1247">`urls` host configuration key</span></span>
* <span data-ttu-id="acc26-1248">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="acc26-1248">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="acc26-1249">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="acc26-1249">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="acc26-1250">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="acc26-1250">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="acc26-1251">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1251">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="acc26-1252">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="acc26-1252">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="acc26-1253">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="acc26-1253">IIS endpoint configuration</span></span>

<span data-ttu-id="acc26-1254">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="acc26-1254">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="acc26-1255">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="acc26-1255">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="acc26-1256">传输配置</span><span class="sxs-lookup"><span data-stu-id="acc26-1256">Transport configuration</span></span>

<span data-ttu-id="acc26-1257">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="acc26-1257">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="acc26-1258">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="acc26-1258">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="acc26-1259">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="acc26-1259">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="acc26-1260">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="acc26-1260">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="acc26-1261">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="acc26-1261">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="acc26-1262">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="acc26-1262">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="acc26-1263">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="acc26-1263">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="acc26-1264">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="acc26-1264">URL prefixes</span></span>

<span data-ttu-id="acc26-1265">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="acc26-1265">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="acc26-1266">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="acc26-1266">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="acc26-1267">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="acc26-1267">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="acc26-1268">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="acc26-1268">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="acc26-1269">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="acc26-1269">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="acc26-1270">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="acc26-1270">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="acc26-1271">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="acc26-1271">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="acc26-1272">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="acc26-1272">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="acc26-1273">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="acc26-1273">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="acc26-1274">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="acc26-1274">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="acc26-1275">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="acc26-1275">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="acc26-1276">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1276">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="acc26-1277">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="acc26-1277">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="acc26-1278">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="acc26-1278">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="acc26-1279">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="acc26-1279">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="acc26-1280">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="acc26-1280">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="acc26-1281">主机筛选</span><span class="sxs-lookup"><span data-stu-id="acc26-1281">Host filtering</span></span>

<span data-ttu-id="acc26-1282">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="acc26-1282">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="acc26-1283">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="acc26-1283">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="acc26-1284">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="acc26-1284">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="acc26-1285">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="acc26-1285">`Host` headers aren't validated.</span></span>

<span data-ttu-id="acc26-1286">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="acc26-1286">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="acc26-1287">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1287">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="acc26-1288">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="acc26-1288">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="acc26-1289">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="acc26-1289">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="acc26-1290">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="acc26-1290">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="acc26-1291">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="acc26-1291">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="acc26-1292">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="acc26-1292">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="acc26-1293">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="acc26-1293">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="acc26-1294">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="acc26-1294">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="acc26-1295">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="acc26-1295">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="acc26-1296">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="acc26-1296">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="acc26-1297">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="acc26-1297">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="acc26-1298">HTTP/1.1 请求排出</span><span class="sxs-lookup"><span data-stu-id="acc26-1298">HTTP/1.1 request draining</span></span>

<span data-ttu-id="acc26-1299">打开 HTTP 连接非常耗时。</span><span class="sxs-lookup"><span data-stu-id="acc26-1299">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="acc26-1300">对于 HTTPS 而言，这也是资源密集型。</span><span class="sxs-lookup"><span data-stu-id="acc26-1300">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="acc26-1301">因此，Kestrel 会尝试按 HTTP/1.1 协议重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="acc26-1301">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="acc26-1302">请求正文必须完全使用才能允许重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="acc26-1302">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="acc26-1303">应用不会始终使用请求正文，例如 `POST` 请求，其中服务器返回重定向或 404 响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-1303">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="acc26-1304">在 `POST` 重定向的情况下：</span><span class="sxs-lookup"><span data-stu-id="acc26-1304">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="acc26-1305">客户端可能已发送部分 `POST` 数据。</span><span class="sxs-lookup"><span data-stu-id="acc26-1305">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="acc26-1306">服务器写入 301 响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-1306">The server writes the 301 response.</span></span>
* <span data-ttu-id="acc26-1307">在完全读取上一个请求正文中的 `POST` 数据之前，不能将连接用于新请求。</span><span class="sxs-lookup"><span data-stu-id="acc26-1307">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="acc26-1308">Kestrel 尝试排出请求正文。</span><span class="sxs-lookup"><span data-stu-id="acc26-1308">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="acc26-1309">排出请求正文意味着读取和丢弃数据，而不处理数据。</span><span class="sxs-lookup"><span data-stu-id="acc26-1309">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="acc26-1310">排出过程会在允许重新使用连接与排出任何剩余数据所用的时间之间进行权衡：</span><span class="sxs-lookup"><span data-stu-id="acc26-1310">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="acc26-1311">排出超时为五秒，该值不可配置。</span><span class="sxs-lookup"><span data-stu-id="acc26-1311">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="acc26-1312">如果 `Content-Length` 或 `Transfer-Encoding` 标头指定的所有数据在超时之前都未被读取，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="acc26-1312">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="acc26-1313">有时，你可能想要在写入响应之前或之后立即终止请求。</span><span class="sxs-lookup"><span data-stu-id="acc26-1313">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="acc26-1314">例如，客户端可能具有限制性的数据上限，因此可以优先考虑限制上传的数据。</span><span class="sxs-lookup"><span data-stu-id="acc26-1314">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="acc26-1315">在这种情况下，若要终止请求，请从控制器、Razor 页面或中间件调用 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1315">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="acc26-1316">在调用 `Abort` 时，存在一些注意事项：</span><span class="sxs-lookup"><span data-stu-id="acc26-1316">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="acc26-1317">创建新连接可能会很慢且成本高昂。</span><span class="sxs-lookup"><span data-stu-id="acc26-1317">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="acc26-1318">在连接关闭之前无法保证客户端已读取响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-1318">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="acc26-1319">应极少调用 `Abort`，并且应将此操作留给严重错误情况（而不是常见错误情况）。</span><span class="sxs-lookup"><span data-stu-id="acc26-1319">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="acc26-1320">只有在需要解决特定问题时才调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1320">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="acc26-1321">例如，如果恶意客户端正在尝试 `POST` 数据或在客户端代码中存在导致大量请求的 bug，则调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1321">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="acc26-1322">不要为常见错误情况（如 HTTP 404（找不到））调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="acc26-1322">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="acc26-1323">调用 `Abort` 之前调用 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) 可确保服务器已完成写入响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-1323">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="acc26-1324">但是，客户端行为是不可预测的，在连接中止前它们可能未读取响应。</span><span class="sxs-lookup"><span data-stu-id="acc26-1324">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="acc26-1325">对于 HTTP/2，此过程又有所不同，因为协议支持在不关闭连接的情况下中止单个请求流。</span><span class="sxs-lookup"><span data-stu-id="acc26-1325">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="acc26-1326">五秒排出超时不适用。</span><span class="sxs-lookup"><span data-stu-id="acc26-1326">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="acc26-1327">如果在完成响应后存在任何未读的请求正文数据，则服务器会发送 HTTP/2 RST 帧。</span><span class="sxs-lookup"><span data-stu-id="acc26-1327">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="acc26-1328">其他请求正文数据帧将被忽略。</span><span class="sxs-lookup"><span data-stu-id="acc26-1328">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="acc26-1329">如果可能，客户端最好使用 [Expect:100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 请求标头，然后等到服务器响应后再开始发送请求正文。</span><span class="sxs-lookup"><span data-stu-id="acc26-1329">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="acc26-1330">这样，客户端便有机会在发送不需要的数据之前检查响应和中止。</span><span class="sxs-lookup"><span data-stu-id="acc26-1330">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="acc26-1331">其他资源</span><span class="sxs-lookup"><span data-stu-id="acc26-1331">Additional resources</span></span>

* <span data-ttu-id="acc26-1332">在 Linux 上使用 UNIX 套接字时，该套接字不会在应用关闭时自动删除。</span><span class="sxs-lookup"><span data-stu-id="acc26-1332">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="acc26-1333">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="acc26-1333">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="acc26-1334">RFC 7230：消息语法和路由（5.4 节：主机）</span><span class="sxs-lookup"><span data-stu-id="acc26-1334">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
