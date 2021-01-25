---
title: 使用 Apache 在 Linux 上托管 ASP.NET Core
author: rick-anderson
description: 了解如何在 CentOS 上将 Apache 设置为反向代理服务器，以将 HTTP 流量重定向到在 Kestrel 上运行的 ASP.NET Core Web 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
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
uid: host-and-deploy/linux-apache
ms.openlocfilehash: e81ad43e1c3b86900848671d9da377a5c04a2a82
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253002"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="34780-103">使用 Apache 在 Linux 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34780-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="34780-104">作者：[Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="34780-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="34780-105">使用本指南，了解如何在 [CentOS 7](https://www.centos.org/) 上将 [Apache](https://httpd.apache.org/) 设置为反向代理服务器，以将 HTTP 流量重定向到在 [Kestrel](xref:fundamentals/servers/kestrel) 服务器上运行的 ASP.NET Core Web 应用。</span><span class="sxs-lookup"><span data-stu-id="34780-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="34780-106">[mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) 和相关模块可创建服务器的反向代理。</span><span class="sxs-lookup"><span data-stu-id="34780-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34780-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="34780-107">Prerequisites</span></span>

* <span data-ttu-id="34780-108">运行 CentOS 7 的服务器，使用具有 sudo 特权的标准用户帐户。</span><span class="sxs-lookup"><span data-stu-id="34780-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="34780-109">在服务器上安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="34780-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="34780-110">访问[下载 .NET Core 页面](https://dotnet.microsoft.com/download/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="34780-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="34780-111">选择最新的 .NET Core 非预览版。</span><span class="sxs-lookup"><span data-stu-id="34780-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="34780-112">在“运行应用”-“运行时”下的表格中，下载最新的非预览版运行时  。</span><span class="sxs-lookup"><span data-stu-id="34780-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="34780-113">选择 Linux 包管理器说明链接，然后按照 CentOS 说明进行操作  。</span><span class="sxs-lookup"><span data-stu-id="34780-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="34780-114">一个现有 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="34780-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="34780-115">升级共享框架后，可随时重启服务器托管的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="34780-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="34780-116">通过应用发布和复制</span><span class="sxs-lookup"><span data-stu-id="34780-116">Publish and copy over the app</span></span>

<span data-ttu-id="34780-117">配置应用以进行[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)。</span><span class="sxs-lookup"><span data-stu-id="34780-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="34780-118">如果应用在本地运行，且未配置为建立安全连接 (HTTPS)，则采用以下任一方法：</span><span class="sxs-lookup"><span data-stu-id="34780-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="34780-119">配置应用，以处理安全的本地连接。</span><span class="sxs-lookup"><span data-stu-id="34780-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="34780-120">有关详细信息，请参阅 [HTTPS 配置](#https-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="34780-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="34780-121">从 Properties/launchSettings.json 文件中的 `applicationUrl` 属性中删除 `https://localhost:5001`（如果存在）  。</span><span class="sxs-lookup"><span data-stu-id="34780-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="34780-122">在开发环境中运行 [dotnet publish](/dotnet/core/tools/dotnet-publish)，将应用打包到可在服务器上运行的目录中（例如 bin/Release/&lt;target_framework_moniker&gt;/publish）  ：</span><span class="sxs-lookup"><span data-stu-id="34780-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="34780-123">如果不希望维护服务器上的 .NET Core 运行时，还可将应用发布为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)。</span><span class="sxs-lookup"><span data-stu-id="34780-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="34780-124">使用集成到组织工作流的工具（例如 SCP、SFTP）将 ASP.NET Core 应用复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="34780-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="34780-125">通常可在 var 目录（例如 var/www/helloapp）下找到 Web 应用   。</span><span class="sxs-lookup"><span data-stu-id="34780-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="34780-126">在生产部署方案中，持续集成工作流会执行发布应用并将资产复制到服务器的工作。</span><span class="sxs-lookup"><span data-stu-id="34780-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="34780-127">配置代理服务器</span><span class="sxs-lookup"><span data-stu-id="34780-127">Configure a proxy server</span></span>

<span data-ttu-id="34780-128">反向代理是为动态 Web 应用提供服务的常见设置。</span><span class="sxs-lookup"><span data-stu-id="34780-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="34780-129">反向代理终止 HTTP 请求，并将其转发到 ASP.NET 应用。</span><span class="sxs-lookup"><span data-stu-id="34780-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="34780-130">代理服务器将客户端请求转发到另一个服务器，而不是自身实现这些请求。</span><span class="sxs-lookup"><span data-stu-id="34780-130">A proxy server forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="34780-131">反向代理转发到固定的目标，通常代表任意客户端。</span><span class="sxs-lookup"><span data-stu-id="34780-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="34780-132">在本指南中，Apache 被配置为反向代理，在 Kestrel 为 ASP.NET Core 应用提供服务的同一服务器上运行。</span><span class="sxs-lookup"><span data-stu-id="34780-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="34780-133">由于请求是通过反向代理转接的，因此使用 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 包中的[转接头中间件](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="34780-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="34780-134">此中间件使用 `X-Forwarded-Proto` 标头来更新 `Request.Scheme`，使重定向 URI 和其他安全策略能够正常工作。</span><span class="sxs-lookup"><span data-stu-id="34780-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="34780-135">调用转接头中间件后，必须放置依赖于该架构的组件，例如身份验证、链接生成、重定向和地理位置。</span><span class="sxs-lookup"><span data-stu-id="34780-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="34780-136">调用其他中间件之前，请先在 `Startup.Configure` 的基础上调用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="34780-136">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="34780-137">配置中间件以转接 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头：</span><span class="sxs-lookup"><span data-stu-id="34780-137">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="34780-138">如果没有为中间件指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，则要转接的默认标头为 `None`。</span><span class="sxs-lookup"><span data-stu-id="34780-138">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="34780-139">默认情况下，在环回地址 (`127.0.0.0/8, [::1]`)（包括标准 localhost 地址 (127.0.0.1)）上运行的代理受信任。</span><span class="sxs-lookup"><span data-stu-id="34780-139">Proxies running on loopback addresses (`127.0.0.0/8, [::1]`), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="34780-140">如果组织内的其他受信任代理或网络处理 Internet 与 Web 服务器之间的请求，请使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 将其添加到 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> 或 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> 的列表。</span><span class="sxs-lookup"><span data-stu-id="34780-140">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="34780-141">以下示例会将 IP 地址为 10.0.0.100 的受信任代理服务器添加到 `Startup.ConfigureServices` 中的转接头中间件 `KnownProxies`：</span><span class="sxs-lookup"><span data-stu-id="34780-141">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="34780-142">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="34780-142">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="34780-143">安装 Apache</span><span class="sxs-lookup"><span data-stu-id="34780-143">Install Apache</span></span>

<span data-ttu-id="34780-144">将 CentOS 包更新为其最新稳定版本：</span><span class="sxs-lookup"><span data-stu-id="34780-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="34780-145">使用单个 `yum` 命令在 CentOS 上安装 Apache Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="34780-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="34780-146">运行该命令后的示例输出：</span><span class="sxs-lookup"><span data-stu-id="34780-146">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="34780-147">在此示例中，输出反映了 httpd.86_64，因为 CentOS 7 版本是 64 位的。</span><span class="sxs-lookup"><span data-stu-id="34780-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="34780-148">若要验证 Apache 的安装位置，请从命令提示符运行 `whereis httpd`。</span><span class="sxs-lookup"><span data-stu-id="34780-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="34780-149">配置 Apache</span><span class="sxs-lookup"><span data-stu-id="34780-149">Configure Apache</span></span>

<span data-ttu-id="34780-150">Apache 的配置文件位于 `/etc/httpd/conf.d/` 目录内。</span><span class="sxs-lookup"><span data-stu-id="34780-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="34780-151">除了 `/etc/httpd/conf.modules.d/` 中的模块配置文件外（其中包含加载模块所需的任何配置文件），将对任何带 .conf  扩展名的文件按字母顺序进行处理。</span><span class="sxs-lookup"><span data-stu-id="34780-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="34780-152">为应用创建名为 helloapp.conf  的配置文件：</span><span class="sxs-lookup"><span data-stu-id="34780-152">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="34780-153">`VirtualHost` 块可以在服务器上的一个或多个文件中多次出现。</span><span class="sxs-lookup"><span data-stu-id="34780-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="34780-154">在前面的配置文件中，Apache 接受端口 80 上的公共流量。</span><span class="sxs-lookup"><span data-stu-id="34780-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="34780-155">正在向域 `www.example.com` 提供服务，`*.example.com` 别名解析为同一网站。</span><span class="sxs-lookup"><span data-stu-id="34780-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="34780-156">有关详细信息，请参阅[基于名称的虚拟主机支持](https://httpd.apache.org/docs/current/vhosts/name-based.html)。</span><span class="sxs-lookup"><span data-stu-id="34780-156">For more information, see [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span></span> <span data-ttu-id="34780-157">请求会通过代理从根位置转到 127.0.0.1 处的服务器的端口 5000。</span><span class="sxs-lookup"><span data-stu-id="34780-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="34780-158">对于双向通信，需要 `ProxyPass` 和 `ProxyPassReverse`。</span><span class="sxs-lookup"><span data-stu-id="34780-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="34780-159">若要更改 Kestrel 的 IP/端口，请参阅 [Kestrel：终结点配置](xref:fundamentals/servers/kestrel/endpoints)。</span><span class="sxs-lookup"><span data-stu-id="34780-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="34780-160">`VirtualHost` 块可以在服务器上的一个或多个文件中多次出现。</span><span class="sxs-lookup"><span data-stu-id="34780-160">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="34780-161">在前面的配置文件中，Apache 接受端口 80 上的公共流量。</span><span class="sxs-lookup"><span data-stu-id="34780-161">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="34780-162">正在向域 `www.example.com` 提供服务，`*.example.com` 别名解析为同一网站。</span><span class="sxs-lookup"><span data-stu-id="34780-162">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="34780-163">有关详细信息，请参阅[基于名称的虚拟主机支持](https://httpd.apache.org/docs/current/vhosts/name-based.html)。</span><span class="sxs-lookup"><span data-stu-id="34780-163">For more information, see [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span></span> <span data-ttu-id="34780-164">请求会通过代理从根位置转到 127.0.0.1 处的服务器的端口 5000。</span><span class="sxs-lookup"><span data-stu-id="34780-164">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="34780-165">对于双向通信，需要 `ProxyPass` 和 `ProxyPassReverse`。</span><span class="sxs-lookup"><span data-stu-id="34780-165">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="34780-166">若要更改 Kestrel 的 IP/端口，请参阅 [Kestrel：终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="34780-166">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

::: moniker-end

> [!WARNING]
> <span data-ttu-id="34780-167">未能指定 VirtualHost  块中的正确 [ServerName 指令](https://httpd.apache.org/docs/current/mod/core.html#servername)会公开应用的安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="34780-167">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="34780-168">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.example.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="34780-168">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="34780-169">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="34780-169">For more information, see [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

<span data-ttu-id="34780-170">可以使用 `ErrorLog` 和 `CustomLog` 指令配置每个 `VirtualHost` 的日志记录。</span><span class="sxs-lookup"><span data-stu-id="34780-170">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="34780-171">`ErrorLog` 是服务器记录错误的位置，`CustomLog` 则设置文件名和日志文件的格式。</span><span class="sxs-lookup"><span data-stu-id="34780-171">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="34780-172">在这种情况下，这是记录请求信息的位置。</span><span class="sxs-lookup"><span data-stu-id="34780-172">In this case, this is where request information is logged.</span></span> <span data-ttu-id="34780-173">每个请求将各占一行。</span><span class="sxs-lookup"><span data-stu-id="34780-173">There's one line for each request.</span></span>

<span data-ttu-id="34780-174">保存文件，并测试配置。</span><span class="sxs-lookup"><span data-stu-id="34780-174">Save the file and test the configuration.</span></span> <span data-ttu-id="34780-175">如果一切正常，响应应为 `Syntax [OK]`。</span><span class="sxs-lookup"><span data-stu-id="34780-175">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="34780-176">重新启动 Apache：</span><span class="sxs-lookup"><span data-stu-id="34780-176">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="34780-177">监视应用</span><span class="sxs-lookup"><span data-stu-id="34780-177">Monitor the app</span></span>

<span data-ttu-id="34780-178">Apache 现在已设置为将对 `http://localhost:80` 发起的请求转发到在 `http://127.0.0.1:5000` 中的 Kestrel 上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="34780-178">Apache is now set up to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="34780-179">但是，未将 Apache 设置为管理 Kestrel 进程。</span><span class="sxs-lookup"><span data-stu-id="34780-179">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="34780-180">使用 systemd  ，并创建服务文件以启动和监视基础 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="34780-180">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="34780-181">systemd  是一个 init 系统，可以提供用于启动、停止和管理进程的许多强大的功能。</span><span class="sxs-lookup"><span data-stu-id="34780-181">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="34780-182">创建服务文件</span><span class="sxs-lookup"><span data-stu-id="34780-182">Create the service file</span></span>

<span data-ttu-id="34780-183">创建服务定义文件：</span><span class="sxs-lookup"><span data-stu-id="34780-183">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="34780-184">应用的一个示例服务文件：</span><span class="sxs-lookup"><span data-stu-id="34780-184">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="34780-185">在前面的示例中，管理服务的用户由 `User` 选项指定。</span><span class="sxs-lookup"><span data-stu-id="34780-185">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="34780-186">用户 (`apache`) 必须存在并且拥有正确应用文件的所有权。</span><span class="sxs-lookup"><span data-stu-id="34780-186">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="34780-187">使用 `TimeoutStopSec` 配置在收到初始中断信号后等待应用程序关闭的持续时间。</span><span class="sxs-lookup"><span data-stu-id="34780-187">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="34780-188">如果应用程序在此时间段内未关闭，则将发出 SIGKILL 以终止该应用程序。</span><span class="sxs-lookup"><span data-stu-id="34780-188">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="34780-189">提供作为无单位秒数的值（例如，`150`）、时间跨度值（例如，`2min 30s`）或 `infinity` 以禁用超时。</span><span class="sxs-lookup"><span data-stu-id="34780-189">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="34780-190">`TimeoutStopSec` 默认为管理器配置文件（*systemd-system.conf*、*system.conf.d*、*systemd-user.conf*、*user.conf.d*）中 `DefaultTimeoutStopSec` 的值。</span><span class="sxs-lookup"><span data-stu-id="34780-190">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="34780-191">大多数分发版的默认超时时间为 90 秒。</span><span class="sxs-lookup"><span data-stu-id="34780-191">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="34780-192">必须转义某些值（例如，SQL 连接字符串）以供配置提供程序读取环境变量。</span><span class="sxs-lookup"><span data-stu-id="34780-192">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="34780-193">使用以下命令生成适当的转义值以供在配置文件中使用：</span><span class="sxs-lookup"><span data-stu-id="34780-193">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="34780-194">环境变量名不支持冒号 (`:`) 分隔符。</span><span class="sxs-lookup"><span data-stu-id="34780-194">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="34780-195">使用双下划线 (`__`) 代替冒号。</span><span class="sxs-lookup"><span data-stu-id="34780-195">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="34780-196">环境变量读入配置时，[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)将双下划线转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="34780-196">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="34780-197">以下示例中，连接字符串密钥 `ConnectionStrings:DefaultConnection` 以 `ConnectionStrings__DefaultConnection` 形式设置到服务定义文件中：</span><span class="sxs-lookup"><span data-stu-id="34780-197">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34780-198">环境变量名不支持冒号 (`:`) 分隔符。</span><span class="sxs-lookup"><span data-stu-id="34780-198">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="34780-199">使用双下划线 (`__`) 代替冒号。</span><span class="sxs-lookup"><span data-stu-id="34780-199">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="34780-200">环境变量读入配置时，[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables)将双下划线转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="34780-200">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="34780-201">以下示例中，连接字符串密钥 `ConnectionStrings:DefaultConnection` 以 `ConnectionStrings__DefaultConnection` 形式设置到服务定义文件中：</span><span class="sxs-lookup"><span data-stu-id="34780-201">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="34780-202">保存该文件并启用该服务：</span><span class="sxs-lookup"><span data-stu-id="34780-202">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="34780-203">启动该服务，并确认它正在运行：</span><span class="sxs-lookup"><span data-stu-id="34780-203">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="34780-204">在配置了反向代理并通过 systemd  管理 Kestrel 后，Web 应用现已完全配置，并能在本地计算机上的浏览器中从 `http://localhost` 进行访问。</span><span class="sxs-lookup"><span data-stu-id="34780-204">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="34780-205">检查响应标头，服务器  标头表示 ASP.NET Core 应用由 Kestrel 提供服务：</span><span class="sxs-lookup"><span data-stu-id="34780-205">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="34780-206">查看日志</span><span class="sxs-lookup"><span data-stu-id="34780-206">View logs</span></span>

<span data-ttu-id="34780-207">由于使用 Kestrel 的 Web 应用是通过 systemd  进行管理的，因此事件和进程将记录到集中日志。</span><span class="sxs-lookup"><span data-stu-id="34780-207">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="34780-208">但是，此日志包含由 systemd  管理的所有服务和进程的条目。</span><span class="sxs-lookup"><span data-stu-id="34780-208">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="34780-209">若要查看特定于 `kestrel-helloapp.service` 的项，请使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="34780-209">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="34780-210">若要进行时间筛选，请使用命令指定时间选项。</span><span class="sxs-lookup"><span data-stu-id="34780-210">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="34780-211">例如，使用 `--since today` 筛选出当天或 `--until 1 hour ago` 来查看前一小时的条目。</span><span class="sxs-lookup"><span data-stu-id="34780-211">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="34780-212">有关详细信息，请参阅 [journalctl 手册页](https://www.unix.com/man-page/centos/1/journalctl/)。</span><span class="sxs-lookup"><span data-stu-id="34780-212">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="34780-213">数据保护</span><span class="sxs-lookup"><span data-stu-id="34780-213">Data protection</span></span>

<span data-ttu-id="34780-214">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)（包括 cookie 中间件等身份验证中间件）和跨站点请求伪造 (CSRF) 保护使用。</span><span class="sxs-lookup"><span data-stu-id="34780-214">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="34780-215">即使用户代码不调用数据保护 API，也应该配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="34780-215">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="34780-216">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="34780-216">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="34780-217">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="34780-217">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="34780-218">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="34780-218">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="34780-219">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="34780-219">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="34780-220">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="34780-220">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="34780-221">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="34780-221">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="34780-222">若要配置数据保护以持久保存并加密密钥环，请参阅：</span><span class="sxs-lookup"><span data-stu-id="34780-222">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="34780-223">保护应用</span><span class="sxs-lookup"><span data-stu-id="34780-223">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="34780-224">配置防火墙</span><span class="sxs-lookup"><span data-stu-id="34780-224">Configure firewall</span></span>

<span data-ttu-id="34780-225">*Firewalld* 是管理防火墙的动态守护程序，支持网络区域。</span><span class="sxs-lookup"><span data-stu-id="34780-225">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="34780-226">仍可以使用 iptable 管理端口和数据包筛选。</span><span class="sxs-lookup"><span data-stu-id="34780-226">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="34780-227">默认情况下应安装 *Firewalld*。</span><span class="sxs-lookup"><span data-stu-id="34780-227">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="34780-228">`yum` 可用于安装包或验证是否已安装。</span><span class="sxs-lookup"><span data-stu-id="34780-228">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="34780-229">使用 `firewalld` 仅打开应用所需的端口。</span><span class="sxs-lookup"><span data-stu-id="34780-229">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="34780-230">本例中使用的是端口 80 和 443。</span><span class="sxs-lookup"><span data-stu-id="34780-230">In this case, ports 80 and 443 are used.</span></span> <span data-ttu-id="34780-231">以下命令将端口 80 和 443 永久设置为打开：</span><span class="sxs-lookup"><span data-stu-id="34780-231">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="34780-232">重新加载防火墙设置。</span><span class="sxs-lookup"><span data-stu-id="34780-232">Reload the firewall settings.</span></span> <span data-ttu-id="34780-233">检查默认区域中可用的服务和端口。</span><span class="sxs-lookup"><span data-stu-id="34780-233">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="34780-234">通过检查 `firewall-cmd -h` 获取可用选项。</span><span class="sxs-lookup"><span data-stu-id="34780-234">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="34780-235">HTTPS 配置</span><span class="sxs-lookup"><span data-stu-id="34780-235">HTTPS configuration</span></span>

<span data-ttu-id="34780-236">配置应用，以进行安全的 (HTTPS) 本地连接</span><span class="sxs-lookup"><span data-stu-id="34780-236">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="34780-237">[dotnet run](/dotnet/core/tools/dotnet-run) 命令使用应用的 Properties/launchSettings.json 文件，该文件将应用配置为侦听 `applicationUrl` 属性（例如 `https://localhost:5001;http://localhost:5000`）提供的 URL。</span><span class="sxs-lookup"><span data-stu-id="34780-237">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="34780-238">使用以下方法之一配置应用，使其在开发过程中将证书用于 `dotnet run` 命令或开发环境（Visual Studio Code 中的 F5 或 Ctrl+F5）：</span><span class="sxs-lookup"><span data-stu-id="34780-238">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="34780-239">[从配置中替换默认证书](xref:fundamentals/servers/kestrel/endpoints#configuration)（推荐）</span><span class="sxs-lookup"><span data-stu-id="34780-239">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel/endpoints#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="34780-240">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="34780-240">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="34780-241">[从配置中替换默认证书](xref:fundamentals/servers/kestrel#configuration)（推荐）</span><span class="sxs-lookup"><span data-stu-id="34780-241">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="34780-242">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="34780-242">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

<span data-ttu-id="34780-243">配置反向代理，以便进行安全 (HTTPS) 客户端连接 </span><span class="sxs-lookup"><span data-stu-id="34780-243">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="34780-244">若要为 Apache 配置 HTTPS，请使用 mod_ssl  模块。</span><span class="sxs-lookup"><span data-stu-id="34780-244">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="34780-245">安装了 httpd  模块时，也会安装了 mod_ssl  模块。</span><span class="sxs-lookup"><span data-stu-id="34780-245">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="34780-246">如果未安装，请使用 `yum` 将其添加到配置。</span><span class="sxs-lookup"><span data-stu-id="34780-246">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="34780-247">若要强制使用 HTTPS，请安装 `mod_rewrite` 模块以启用 URL 重写：</span><span class="sxs-lookup"><span data-stu-id="34780-247">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="34780-248">修改 helloapp.conf  文件以启用 URL 重写和端口 443 上的安全通信：</span><span class="sxs-lookup"><span data-stu-id="34780-248">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="34780-249">此示例中使用了本地生成的证书。</span><span class="sxs-lookup"><span data-stu-id="34780-249">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="34780-250">SSLCertificateFile  应为域名的主证书文件。</span><span class="sxs-lookup"><span data-stu-id="34780-250">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="34780-251">SSLCertificateKeyFile  应为创建 CSR 时生成的密钥文件。</span><span class="sxs-lookup"><span data-stu-id="34780-251">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="34780-252">SSLCertificateChainFile  应为证书颁发机构提供的中间证书文件（如有）。</span><span class="sxs-lookup"><span data-stu-id="34780-252">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="34780-253">保存文件，并测试配置：</span><span class="sxs-lookup"><span data-stu-id="34780-253">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="34780-254">重新启动 Apache：</span><span class="sxs-lookup"><span data-stu-id="34780-254">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="34780-255">其他 Apache 建议</span><span class="sxs-lookup"><span data-stu-id="34780-255">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="34780-256">通过共享框架更新重启应用</span><span class="sxs-lookup"><span data-stu-id="34780-256">Restart apps with shared framework updates</span></span>

<span data-ttu-id="34780-257">在服务器上升级共享框架后，重启服务器托管的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="34780-257">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="34780-258">其他标头</span><span class="sxs-lookup"><span data-stu-id="34780-258">Additional headers</span></span>

<span data-ttu-id="34780-259">为了防止恶意攻击，应对一些标头进行修改或添加一些标头。</span><span class="sxs-lookup"><span data-stu-id="34780-259">To secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="34780-260">确保已安装 `mod_headers` 模块：</span><span class="sxs-lookup"><span data-stu-id="34780-260">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="34780-261">保护 Apache 免受点击劫持攻击</span><span class="sxs-lookup"><span data-stu-id="34780-261">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="34780-262">[点击劫持](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)（也称为 *UI 伪装攻击*）是一种恶意攻击，其中网站访问者会上当受骗，从而导致在与当前要访问的页面不同的页面上单击链接或按钮。</span><span class="sxs-lookup"><span data-stu-id="34780-262">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="34780-263">使用 `X-FRAME-OPTIONS` 可保护网站。</span><span class="sxs-lookup"><span data-stu-id="34780-263">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="34780-264">缓解点击劫持攻击：</span><span class="sxs-lookup"><span data-stu-id="34780-264">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="34780-265">编辑 *httpd.conf* 文件：</span><span class="sxs-lookup"><span data-stu-id="34780-265">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="34780-266">添加行 `Header append X-FRAME-OPTIONS "SAMEORIGIN"`。</span><span class="sxs-lookup"><span data-stu-id="34780-266">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="34780-267">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="34780-267">Save the file.</span></span>
1. <span data-ttu-id="34780-268">重启 Apache。</span><span class="sxs-lookup"><span data-stu-id="34780-268">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="34780-269">MIME 类型探查</span><span class="sxs-lookup"><span data-stu-id="34780-269">MIME-type sniffing</span></span>

<span data-ttu-id="34780-270">`X-Content-Type-Options` 标头阻止 Internet Explorer 进行 *MIME 探查*（从文件内容中确定文件的 `Content-Type`）。</span><span class="sxs-lookup"><span data-stu-id="34780-270">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="34780-271">如果服务器通过设置 `nosniff` 选项将 `Content-Type` 标头设置为 `text/html`，则不管文件内容为何，Internet Explorer 都会将内容呈现为 `text/html`。</span><span class="sxs-lookup"><span data-stu-id="34780-271">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="34780-272">编辑 *httpd.conf* 文件：</span><span class="sxs-lookup"><span data-stu-id="34780-272">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="34780-273">添加行 `Header set X-Content-Type-Options "nosniff"`。</span><span class="sxs-lookup"><span data-stu-id="34780-273">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="34780-274">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="34780-274">Save the file.</span></span> <span data-ttu-id="34780-275">重启 Apache。</span><span class="sxs-lookup"><span data-stu-id="34780-275">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="34780-276">负载平衡</span><span class="sxs-lookup"><span data-stu-id="34780-276">Load Balancing</span></span>

<span data-ttu-id="34780-277">此示例演示如何在同一实例计算机上的 CentOS 7 和 Kestrel 上设置和配置 Apache。</span><span class="sxs-lookup"><span data-stu-id="34780-277">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="34780-278">为了不出现单一故障点，使用 mod_proxy_balancer 并修改 VirtualHost 可实现在 Apache 代理服务器后方管理 Web 应用的多个实例。</span><span class="sxs-lookup"><span data-stu-id="34780-278">To not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="34780-279">在下面所示的配置文件中，`helloapp` 的其他实例设置为在端口 5001 上运行。</span><span class="sxs-lookup"><span data-stu-id="34780-279">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="34780-280">“代理”  部分设置了具有两个成员的均衡器配置，以便对 byrequests  进行负载均衡。</span><span class="sxs-lookup"><span data-stu-id="34780-280">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="34780-281">速率限制</span><span class="sxs-lookup"><span data-stu-id="34780-281">Rate Limits</span></span>

<span data-ttu-id="34780-282">使用 httpd  模块中包含的 mod_ratelimit  ，客户端的带宽可以限制为：</span><span class="sxs-lookup"><span data-stu-id="34780-282">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="34780-283">示例文件将根位置下的带宽限制为 600 KB/秒：</span><span class="sxs-lookup"><span data-stu-id="34780-283">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="34780-284">较长的请求标头字段</span><span class="sxs-lookup"><span data-stu-id="34780-284">Long request header fields</span></span>

<span data-ttu-id="34780-285">代理服务器默认设置通常将请求标头字段限制为 8190 字节。</span><span class="sxs-lookup"><span data-stu-id="34780-285">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="34780-286">某些应用可能需要超过默认值的字段（例如，使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 的应用）。</span><span class="sxs-lookup"><span data-stu-id="34780-286">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="34780-287">如果需要更长的字段，则代理服务器的 [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) 指令需要进行调整。</span><span class="sxs-lookup"><span data-stu-id="34780-287">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="34780-288">要应用的值具体取决于方案。</span><span class="sxs-lookup"><span data-stu-id="34780-288">The value to apply depends on the scenario.</span></span> <span data-ttu-id="34780-289">有关详细信息，请参见服务器文档。</span><span class="sxs-lookup"><span data-stu-id="34780-289">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="34780-290">除非必要，否则不要提高 `LimitRequestFieldSize` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="34780-290">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="34780-291">提高该值将增加缓冲区溢出的风险和恶意用户的拒绝服务 (DoS) 攻击风险。</span><span class="sxs-lookup"><span data-stu-id="34780-291">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34780-292">其他资源</span><span class="sxs-lookup"><span data-stu-id="34780-292">Additional resources</span></span>

* [<span data-ttu-id="34780-293">Linux 上 .NET Core 的先决条件</span><span class="sxs-lookup"><span data-stu-id="34780-293">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
