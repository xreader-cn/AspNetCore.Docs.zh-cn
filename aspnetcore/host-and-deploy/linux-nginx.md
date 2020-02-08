---
title: 使用 Nginx 在 Linux 上托管 ASP.NET Core
author: guardrex
description: 了解如何在 Ubuntu 16.04 上将 Nginx 设置为反向代理，从而将 HTTP 流量转发到在 Kestrel 上运行的 ASP.NET Core Web 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 1a83b7d1b211862793e3ba086234b97248f9ae70
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928492"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="c402b-103">使用 Nginx 在 Linux 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c402b-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="c402b-104">作者：[Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="c402b-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="c402b-105">本指南介绍如何在 Ubuntu 16.04 服务器上设置生产就绪 ASP.NET Core 环境。</span><span class="sxs-lookup"><span data-stu-id="c402b-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="c402b-106">这些说明可能适用于较新版本的 Ubuntu，但尚未使用较新版本进行测试。</span><span class="sxs-lookup"><span data-stu-id="c402b-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="c402b-107">有关 ASP.NET Core 支持的其他 Linux 分配的信息，请参阅 [Linux 上 .NET Core 的先决条件](/dotnet/core/linux-prerequisites)。</span><span class="sxs-lookup"><span data-stu-id="c402b-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="c402b-108">对于 Ubuntu 14.04，建议进行监控，以此作为监视 Kestrel 进程的解决方案。</span><span class="sxs-lookup"><span data-stu-id="c402b-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="c402b-109">在 Ubuntu 14.04 上不提供 systemd。</span><span class="sxs-lookup"><span data-stu-id="c402b-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="c402b-110">有关 Ubuntu 14.04 的说明，请参阅[本主题的以前版本](https://github.com/aspnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)。</span><span class="sxs-lookup"><span data-stu-id="c402b-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/aspnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="c402b-111">本指南：</span><span class="sxs-lookup"><span data-stu-id="c402b-111">This guide:</span></span>

* <span data-ttu-id="c402b-112">将现有 ASP.NET Core 应用置于反向代理服务器后方。</span><span class="sxs-lookup"><span data-stu-id="c402b-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="c402b-113">设置反向代理服务器，以便将请求转发到 Kestrel Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="c402b-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="c402b-114">确保 Web 应用在启动时作为守护程序运行。</span><span class="sxs-lookup"><span data-stu-id="c402b-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="c402b-115">配置进程管理工具以帮助重新启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="c402b-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c402b-116">先决条件</span><span class="sxs-lookup"><span data-stu-id="c402b-116">Prerequisites</span></span>

1. <span data-ttu-id="c402b-117">使用具有 sudo 特权的标准用户帐户访问 Ubuntu 16.04 服务器。</span><span class="sxs-lookup"><span data-stu-id="c402b-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="c402b-118">在服务器上安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="c402b-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="c402b-119">访问[下载 .NET Core 页面](https://dotnet.microsoft.com/download/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="c402b-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="c402b-120">选择最新的 .NET Core 非预览版。</span><span class="sxs-lookup"><span data-stu-id="c402b-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="c402b-121">在“运行应用”-“运行时”下的表格中，下载最新的非预览版运行时。</span><span class="sxs-lookup"><span data-stu-id="c402b-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="c402b-122">选择 Linux 包管理器说明链接，然后按照 Ubuntu 版本的 Ubuntu 说明进行操作。</span><span class="sxs-lookup"><span data-stu-id="c402b-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="c402b-123">一个现有 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c402b-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="c402b-124">升级共享框架后，可随时重启服务器托管的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c402b-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="c402b-125">通过应用发布和复制</span><span class="sxs-lookup"><span data-stu-id="c402b-125">Publish and copy over the app</span></span>

<span data-ttu-id="c402b-126">配置应用以进行[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)。</span><span class="sxs-lookup"><span data-stu-id="c402b-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="c402b-127">如果应用在本地运行，且未配置为建立安全连接 (HTTPS)，则采用以下任一方法：</span><span class="sxs-lookup"><span data-stu-id="c402b-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="c402b-128">配置应用，以处理安全的本地连接。</span><span class="sxs-lookup"><span data-stu-id="c402b-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="c402b-129">有关详细信息，请参阅 [HTTPS 配置](#https-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="c402b-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="c402b-130">从 Properties/launchSettings.json 文件中的 `applicationUrl` 属性中删除 `https://localhost:5001`（如果存在）。</span><span class="sxs-lookup"><span data-stu-id="c402b-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="c402b-131">在开发环境中运行 [dotnet publish](/dotnet/core/tools/dotnet-publish)，将应用打包到可在服务器上运行的目录中（例如 bin/Release/&lt;target_framework_moniker&gt;/publish）：</span><span class="sxs-lookup"><span data-stu-id="c402b-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="c402b-132">如果不希望维护服务器上的 .NET Core 运行时，还可将应用发布为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)。</span><span class="sxs-lookup"><span data-stu-id="c402b-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="c402b-133">使用集成到组织工作流的工具（例如 SCP、SFTP）将 ASP.NET Core 应用复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="c402b-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="c402b-134">通常可在 var 目录（例如 var/www/helloapp）下找到 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="c402b-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="c402b-135">在生产部署方案中，持续集成工作流会执行发布应用并将资产复制到服务器的工作。</span><span class="sxs-lookup"><span data-stu-id="c402b-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="c402b-136">测试应用：</span><span class="sxs-lookup"><span data-stu-id="c402b-136">Test the app:</span></span>

1. <span data-ttu-id="c402b-137">在命令行中运行应用：`dotnet <app_assembly>.dll`。</span><span class="sxs-lookup"><span data-stu-id="c402b-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="c402b-138">在浏览器中，导航到 `http://<serveraddress>:<port>` 以确认应用在 Linux 本地正常运行。</span><span class="sxs-lookup"><span data-stu-id="c402b-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="c402b-139">配置反向代理服务器</span><span class="sxs-lookup"><span data-stu-id="c402b-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="c402b-140">反向代理是为动态 Web 应用提供服务的常见设置。</span><span class="sxs-lookup"><span data-stu-id="c402b-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="c402b-141">反向代理终止 HTTP 请求，并将其转发到 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c402b-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="c402b-142">使用反向代理服务器</span><span class="sxs-lookup"><span data-stu-id="c402b-142">Use a reverse proxy server</span></span>

<span data-ttu-id="c402b-143">Kestrel 非常适合从 ASP.NET Core 提供动态内容。</span><span class="sxs-lookup"><span data-stu-id="c402b-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="c402b-144">但是，Web 服务功能不像服务器（如 IIS、Apache 或 Nginx）那样功能丰富。</span><span class="sxs-lookup"><span data-stu-id="c402b-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="c402b-145">反向代理服务器可以卸载 HTTP 服务器的工作负载，如提供静态内容、缓存请求、压缩请求和 HTTPS 终端。</span><span class="sxs-lookup"><span data-stu-id="c402b-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="c402b-146">反向代理服务器可能驻留在专用计算机上，也可能与 HTTP 服务器一起部署。</span><span class="sxs-lookup"><span data-stu-id="c402b-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="c402b-147">鉴于此指南的目的，使用 Nginx 的单个实例。</span><span class="sxs-lookup"><span data-stu-id="c402b-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="c402b-148">它与 HTTP 服务器一起运行在同一服务器上。</span><span class="sxs-lookup"><span data-stu-id="c402b-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="c402b-149">根据要求，可以选择不同的设置。</span><span class="sxs-lookup"><span data-stu-id="c402b-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="c402b-150">由于请求是通过反向代理转接的，因此使用 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 包中的[转接头中间件](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="c402b-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="c402b-151">此中间件使用 `X-Forwarded-Proto` 标头来更新 `Request.Scheme`，使重定向 URI 和其他安全策略能够正常工作。</span><span class="sxs-lookup"><span data-stu-id="c402b-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="c402b-152">调用转接头中间件后，必须放置依赖于该架构的组件，例如身份验证、链接生成、重定向和地理位置。</span><span class="sxs-lookup"><span data-stu-id="c402b-152">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="c402b-153">作为一般规则，转接头中间件应在诊断和错误处理中间件以外的其他中间件之前运行。</span><span class="sxs-lookup"><span data-stu-id="c402b-153">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="c402b-154">此顺序可确保依赖于转接头信息的中间件可以使用标头值进行处理。</span><span class="sxs-lookup"><span data-stu-id="c402b-154">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="c402b-155">在调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 或类似的身份验证方案中间件之前，调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 方法。</span><span class="sxs-lookup"><span data-stu-id="c402b-155">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="c402b-156">配置中间件以转接 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头：</span><span class="sxs-lookup"><span data-stu-id="c402b-156">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="c402b-157">如果没有为中间件指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，则要转接的默认标头为 `None`。</span><span class="sxs-lookup"><span data-stu-id="c402b-157">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="c402b-158">默认情况下，在环回地址 (127.0.0.0/8, [::1])（包括标准 localhost 地址 (127.0.0.1)）上运行的代理受信任。</span><span class="sxs-lookup"><span data-stu-id="c402b-158">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="c402b-159">如果组织内的其他受信任代理或网络处理 Internet 与 Web 服务器之间的请求，请使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 将其添加到 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> 或 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> 的列表。</span><span class="sxs-lookup"><span data-stu-id="c402b-159">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="c402b-160">以下示例会将 IP 地址为 10.0.0.100 的受信任代理服务器添加到 `Startup.ConfigureServices` 中的转接头中间件 `KnownProxies`：</span><span class="sxs-lookup"><span data-stu-id="c402b-160">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="c402b-161">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="c402b-161">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="c402b-162">安装 Nginx</span><span class="sxs-lookup"><span data-stu-id="c402b-162">Install Nginx</span></span>

<span data-ttu-id="c402b-163">使用 `apt-get` 安装 Nginx。</span><span class="sxs-lookup"><span data-stu-id="c402b-163">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="c402b-164">安装程序将创建一个 systemd init 脚本，该脚本运行 Nginx，作为系统启动时的守护程序。</span><span class="sxs-lookup"><span data-stu-id="c402b-164">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="c402b-165">按照以下网站上的 Ubuntu 安装说明操作：[Nginx：官方 Debian/Ubuntu 包](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)。</span><span class="sxs-lookup"><span data-stu-id="c402b-165">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="c402b-166">如果需要可选 Nginx 模块，则可能需要从源代码生成 Nginx。</span><span class="sxs-lookup"><span data-stu-id="c402b-166">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="c402b-167">因为是首次安装 Nginx，通过运行以下命令显式启动：</span><span class="sxs-lookup"><span data-stu-id="c402b-167">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="c402b-168">确认浏览器显示 Nginx 的默认登陆页。</span><span class="sxs-lookup"><span data-stu-id="c402b-168">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="c402b-169">可在 `http://<server_IP_address>/index.nginx-debian.html` 访问登陆页面。</span><span class="sxs-lookup"><span data-stu-id="c402b-169">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="c402b-170">配置 Nginx</span><span class="sxs-lookup"><span data-stu-id="c402b-170">Configure Nginx</span></span>

<span data-ttu-id="c402b-171">若要将 Nginx 配置为反向代理以将请求转接到 ASP.NET Core 应用，请修改 /etc/nginx/sites-available/default。</span><span class="sxs-lookup"><span data-stu-id="c402b-171">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="c402b-172">在文本编辑器中打开它，并将内容替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="c402b-172">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="c402b-173">如果要用是依赖于 SignalR WebSocket 的 Blazor Server 应用，请参阅 <xref:host-and-deploy/blazor/server#linux-with-nginx> 了解如何设置 `Connection` 标头。</span><span class="sxs-lookup"><span data-stu-id="c402b-173">If the app is a Blazor Server app that relies on SignalR WebSockets, see <xref:host-and-deploy/blazor/server#linux-with-nginx> for information on how to set the `Connection` header.</span></span>

<span data-ttu-id="c402b-174">当没有匹配的 `server_name` 时，Nginx 使用默认服务器。</span><span class="sxs-lookup"><span data-stu-id="c402b-174">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="c402b-175">如果没有定义默认服务器，则配置文件中的第一台服务器是默认服务器。</span><span class="sxs-lookup"><span data-stu-id="c402b-175">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="c402b-176">作为最佳做法，添加指定默认服务器，它会在配置文件中返回状态代码 444。</span><span class="sxs-lookup"><span data-stu-id="c402b-176">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="c402b-177">默认的服务器配置示例是：</span><span class="sxs-lookup"><span data-stu-id="c402b-177">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="c402b-178">使用上述配置文件和默认服务器，Nginx 接受主机标头 `example.com` 或 `*.example.com` 端口 80 上的公共流量。</span><span class="sxs-lookup"><span data-stu-id="c402b-178">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="c402b-179">与这些主机不匹配的请求不会转接到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c402b-179">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="c402b-180">Nginx 将匹配的请求转接到 `http://localhost:5000` 中的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c402b-180">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="c402b-181">有关详细信息，请参阅 [nginx 如何处理请求](https://nginx.org/docs/http/request_processing.html)。</span><span class="sxs-lookup"><span data-stu-id="c402b-181">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="c402b-182">若要更改 Kestrel 的 IP/端口，请参阅 [Kestrel：终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="c402b-182">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="c402b-183">未能指定正确的 [server_name 指令](https://nginx.org/docs/http/server_names.html)会公开应用的安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="c402b-183">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="c402b-184">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.example.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="c402b-184">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c402b-185">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="c402b-185">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="c402b-186">完成配置 Nginx 后，运行 `sudo nginx -t` 来验证配置文件的语法。</span><span class="sxs-lookup"><span data-stu-id="c402b-186">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="c402b-187">如果配置文件测试成功，可以通过运行 `sudo nginx -s reload` 强制 Nginx 选取更改。</span><span class="sxs-lookup"><span data-stu-id="c402b-187">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="c402b-188">要直接在服务器上运行应用：</span><span class="sxs-lookup"><span data-stu-id="c402b-188">To directly run the app on the server:</span></span>

1. <span data-ttu-id="c402b-189">请导航到应用目录。</span><span class="sxs-lookup"><span data-stu-id="c402b-189">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="c402b-190">运行应用：`dotnet <app_assembly.dll>`，其中 `app_assembly.dll` 是应用的程序集文件名。</span><span class="sxs-lookup"><span data-stu-id="c402b-190">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="c402b-191">如果应用在服务器上运行，但无法通过 Internet 响应，请检查服务器的防火墙，并确认端口 80 已打开。</span><span class="sxs-lookup"><span data-stu-id="c402b-191">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="c402b-192">如果使用 Azure Ubuntu VM，请添加启用入站端口 80 流量的网络安全组 (NSG) 规则。</span><span class="sxs-lookup"><span data-stu-id="c402b-192">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="c402b-193">不需要启用出站端口 80 规则，因为启用入站规则后会自动许可出站流量。</span><span class="sxs-lookup"><span data-stu-id="c402b-193">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="c402b-194">测试应用完成后，请在命令提示符处按 `Ctrl+C` 关闭应用。</span><span class="sxs-lookup"><span data-stu-id="c402b-194">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="c402b-195">监视应用</span><span class="sxs-lookup"><span data-stu-id="c402b-195">Monitor the app</span></span>

<span data-ttu-id="c402b-196">服务器设置为将对 `http://<serveraddress>:80` 发起的请求转接到在 `http://127.0.0.1:5000` 中的 Kestrel 上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c402b-196">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="c402b-197">但是，未将 Nginx 设置为管理 Kestrel 进程。</span><span class="sxs-lookup"><span data-stu-id="c402b-197">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="c402b-198">systemd 可用于创建服务文件以启动和监视基础 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="c402b-198">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="c402b-199">systemd 是一个 init 系统，可以提供用于启动、停止和管理进程的许多强大的功能。</span><span class="sxs-lookup"><span data-stu-id="c402b-199">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="c402b-200">创建服务文件</span><span class="sxs-lookup"><span data-stu-id="c402b-200">Create the service file</span></span>

<span data-ttu-id="c402b-201">创建服务定义文件：</span><span class="sxs-lookup"><span data-stu-id="c402b-201">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="c402b-202">以下是应用的一个示例服务文件：</span><span class="sxs-lookup"><span data-stu-id="c402b-202">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="c402b-203">在前面的示例中，管理服务的用户由 `User` 选项指定。</span><span class="sxs-lookup"><span data-stu-id="c402b-203">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="c402b-204">用户 (`www-data`) 必须存在并且拥有正确应用文件的所有权。</span><span class="sxs-lookup"><span data-stu-id="c402b-204">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="c402b-205">使用 `TimeoutStopSec` 配置在收到初始中断信号后等待应用程序关闭的持续时间。</span><span class="sxs-lookup"><span data-stu-id="c402b-205">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="c402b-206">如果应用程序在此时间段内未关闭，则将发出 SIGKILL 以终止该应用程序。</span><span class="sxs-lookup"><span data-stu-id="c402b-206">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="c402b-207">提供作为无单位秒数的值（例如，`150`）、时间跨度值（例如，`2min 30s`）或 `infinity` 以禁用超时。</span><span class="sxs-lookup"><span data-stu-id="c402b-207">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="c402b-208">`TimeoutStopSec` 默认为管理器配置文件（*systemd-system.conf*、*system.conf.d*、*systemd-user.conf*、*user.conf.d*）中 `DefaultTimeoutStopSec` 的值。</span><span class="sxs-lookup"><span data-stu-id="c402b-208">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="c402b-209">大多数分发版的默认超时时间为 90 秒。</span><span class="sxs-lookup"><span data-stu-id="c402b-209">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="c402b-210">Linux 具有区分大小写的文件系统。</span><span class="sxs-lookup"><span data-stu-id="c402b-210">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="c402b-211">将 ASPNETCORE_ENVIRONMENT 设置为“生产”会导致搜索配置文件 appsettings.Production.json，而不是 appsettings.production.json。</span><span class="sxs-lookup"><span data-stu-id="c402b-211">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="c402b-212">必须转义某些值（例如，SQL 连接字符串）以供配置提供程序读取环境变量。</span><span class="sxs-lookup"><span data-stu-id="c402b-212">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="c402b-213">使用以下命令生成适当的转义值以供在配置文件中使用：</span><span class="sxs-lookup"><span data-stu-id="c402b-213">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="c402b-214">环境变量名不支持冒号 (`:`) 分隔符。</span><span class="sxs-lookup"><span data-stu-id="c402b-214">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="c402b-215">使用双下划线 (`__`) 代替冒号。</span><span class="sxs-lookup"><span data-stu-id="c402b-215">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="c402b-216">环境变量读入配置时，[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)将双下划线转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="c402b-216">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="c402b-217">以下示例中，连接字符串密钥 `ConnectionStrings:DefaultConnection` 以 `ConnectionStrings__DefaultConnection` 形式设置到服务定义文件中：</span><span class="sxs-lookup"><span data-stu-id="c402b-217">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="c402b-218">保存该文件并启用该服务。</span><span class="sxs-lookup"><span data-stu-id="c402b-218">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="c402b-219">启用该服务，并确认它正在运行。</span><span class="sxs-lookup"><span data-stu-id="c402b-219">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="c402b-220">在配置了反向代理并通过 systemd 管理 Kestrel 后，Web 应用现已完全配置，并能在本地计算机上的浏览器中从 `http://localhost` 进行访问。</span><span class="sxs-lookup"><span data-stu-id="c402b-220">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="c402b-221">也可以从远程计算机进行访问，同时限制可能进行阻止的任何防火墙。</span><span class="sxs-lookup"><span data-stu-id="c402b-221">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="c402b-222">检查响应标头，`Server` 标头显示由 Kestrel 所提供的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c402b-222">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="c402b-223">查看日志</span><span class="sxs-lookup"><span data-stu-id="c402b-223">View logs</span></span>

<span data-ttu-id="c402b-224">使用 Kestrel 的 Web 应用是通过 `systemd` 进行管理的，因此所有事件和进程都被记录到集中日志。</span><span class="sxs-lookup"><span data-stu-id="c402b-224">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="c402b-225">但是，此日志包含由 `systemd` 管理的所有服务和进程的全部条目。</span><span class="sxs-lookup"><span data-stu-id="c402b-225">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="c402b-226">若要查看特定于 `kestrel-helloapp.service` 的项，请使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="c402b-226">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="c402b-227">有关进一步筛选，使用时间选项（如 `--since today`、`--until 1 hour ago`）或这些选项的组合可以减少返回的条目数。</span><span class="sxs-lookup"><span data-stu-id="c402b-227">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="c402b-228">数据保护</span><span class="sxs-lookup"><span data-stu-id="c402b-228">Data protection</span></span>

<span data-ttu-id="c402b-229">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)（包括 cookie 中间件等身份验证中间件）和跨站点请求伪造 (CSRF) 保护使用。</span><span class="sxs-lookup"><span data-stu-id="c402b-229">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="c402b-230">即使用户代码不调用数据保护 API，也应该配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="c402b-230">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="c402b-231">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="c402b-231">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="c402b-232">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="c402b-232">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="c402b-233">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="c402b-233">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="c402b-234">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="c402b-234">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="c402b-235">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="c402b-235">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="c402b-236">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="c402b-236">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="c402b-237">若要配置数据保护以持久保存并加密密钥环，请参阅：</span><span class="sxs-lookup"><span data-stu-id="c402b-237">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="c402b-238">较长的请求标头字段</span><span class="sxs-lookup"><span data-stu-id="c402b-238">Long request header fields</span></span>

<span data-ttu-id="c402b-239">代理服务器默认设置通常将请求标头字段限制为 4 K 或 8 K，具体取决于平台。</span><span class="sxs-lookup"><span data-stu-id="c402b-239">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="c402b-240">某些应用可能需要超过默认值的字段（例如，使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 的应用）。</span><span class="sxs-lookup"><span data-stu-id="c402b-240">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="c402b-241">如果需要更长的字段，则代理服务器的默认设置需要进行调整。</span><span class="sxs-lookup"><span data-stu-id="c402b-241">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="c402b-242">要应用的值具体取决于方案。</span><span class="sxs-lookup"><span data-stu-id="c402b-242">The values to apply depend on the scenario.</span></span> <span data-ttu-id="c402b-243">有关详细信息，请参见服务器文档。</span><span class="sxs-lookup"><span data-stu-id="c402b-243">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="c402b-244">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="c402b-244">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="c402b-245">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="c402b-245">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="c402b-246">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="c402b-246">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="c402b-247">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="c402b-247">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="c402b-248">除非必要，否则不要提高代理缓冲区的默认值。</span><span class="sxs-lookup"><span data-stu-id="c402b-248">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="c402b-249">提高这些值将增加缓冲区溢出的风险和恶意用户的拒绝服务 (DoS) 攻击风险。</span><span class="sxs-lookup"><span data-stu-id="c402b-249">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="c402b-250">保护应用</span><span class="sxs-lookup"><span data-stu-id="c402b-250">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="c402b-251">启用 AppArmor</span><span class="sxs-lookup"><span data-stu-id="c402b-251">Enable AppArmor</span></span>

<span data-ttu-id="c402b-252">Linux 安全模块 (LSM) 是一个框架，它是自 Linux 2.6 后的 Linux kernel 的一部分。</span><span class="sxs-lookup"><span data-stu-id="c402b-252">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="c402b-253">LSM 支持安全模块的不同实现。</span><span class="sxs-lookup"><span data-stu-id="c402b-253">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="c402b-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) 是实现强制访问控制系统的 LSM，它允许将程序限制在一组有限的资源内。</span><span class="sxs-lookup"><span data-stu-id="c402b-254">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="c402b-255">确保已启用并成功配置 AppArmor。</span><span class="sxs-lookup"><span data-stu-id="c402b-255">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="c402b-256">配置防火墙</span><span class="sxs-lookup"><span data-stu-id="c402b-256">Configure the firewall</span></span>

<span data-ttu-id="c402b-257">关闭所有未使用的外部端口。</span><span class="sxs-lookup"><span data-stu-id="c402b-257">Close off all external ports that are not in use.</span></span> <span data-ttu-id="c402b-258">通过为配置防火墙提供 CLI，不复杂的防火墙 (ufw) 为 `iptables` 提供了前端。</span><span class="sxs-lookup"><span data-stu-id="c402b-258">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="c402b-259">如果未正确配置，防火墙将阻止对整个系统的访问。</span><span class="sxs-lookup"><span data-stu-id="c402b-259">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="c402b-260">在使用 SSH 进行连接时，未能指定正确的 SSH 端口最终会将你关在系统之外。</span><span class="sxs-lookup"><span data-stu-id="c402b-260">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="c402b-261">默认端口为 22。</span><span class="sxs-lookup"><span data-stu-id="c402b-261">The default port is 22.</span></span> <span data-ttu-id="c402b-262">有关详细信息，请参阅 [ufw 简介](https://help.ubuntu.com/community/UFW)和[手册](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)。</span><span class="sxs-lookup"><span data-stu-id="c402b-262">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="c402b-263">安装 `ufw`，并将其配置为允许所需任何端口上的流量。</span><span class="sxs-lookup"><span data-stu-id="c402b-263">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="c402b-264">保护 Nginx</span><span class="sxs-lookup"><span data-stu-id="c402b-264">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="c402b-265">更改 Nginx 响应名称</span><span class="sxs-lookup"><span data-stu-id="c402b-265">Change the Nginx response name</span></span>

<span data-ttu-id="c402b-266">编辑 src/http/ngx_http_header_filter_module.c：</span><span class="sxs-lookup"><span data-stu-id="c402b-266">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="c402b-267">配置选项</span><span class="sxs-lookup"><span data-stu-id="c402b-267">Configure options</span></span>

<span data-ttu-id="c402b-268">用其他必需模块配置服务器。</span><span class="sxs-lookup"><span data-stu-id="c402b-268">Configure the server with additional required modules.</span></span> <span data-ttu-id="c402b-269">请考虑使用 [ModSecurity](https://www.modsecurity.org/) 等 Web 应用防火墙来加强对应用的保护。</span><span class="sxs-lookup"><span data-stu-id="c402b-269">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="c402b-270">HTTPS 配置</span><span class="sxs-lookup"><span data-stu-id="c402b-270">HTTPS configuration</span></span>

<span data-ttu-id="c402b-271">配置应用，以进行安全的 (HTTPS) 本地连接</span><span class="sxs-lookup"><span data-stu-id="c402b-271">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="c402b-272">[dotnet run](/dotnet/core/tools/dotnet-run) 命令使用应用的 Properties/launchSettings.json 文件，该文件将应用配置为侦听 `applicationUrl` 属性（例如 `https://localhost:5001;http://localhost:5000`）提供的 URL。</span><span class="sxs-lookup"><span data-stu-id="c402b-272">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="c402b-273">使用以下方法之一配置应用，使其在开发过程中将证书用于 `dotnet run` 命令或开发环境（Visual Studio Code 中的 F5 或 Ctrl+F5）：</span><span class="sxs-lookup"><span data-stu-id="c402b-273">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="c402b-274">[从配置中替换默认证书](xref:fundamentals/servers/kestrel#configuration)（推荐）</span><span class="sxs-lookup"><span data-stu-id="c402b-274">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="c402b-275">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="c402b-275">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="c402b-276">配置反向代理，以便进行安全 (HTTPS) 客户端连接</span><span class="sxs-lookup"><span data-stu-id="c402b-276">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="c402b-277">通过指定由受信任的证书颁发机构 (CA) 颁发的有效证书来配置服务器，以侦听端口 `443` 上的 HTTPS 流量。</span><span class="sxs-lookup"><span data-stu-id="c402b-277">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="c402b-278">通过采用以下“/etc/nginx/nginx.conf”文件中所示的某些做法来增强安全保护。</span><span class="sxs-lookup"><span data-stu-id="c402b-278">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="c402b-279">示例包括选择更强的密码并将通过 HTTP 的所有流量重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c402b-279">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="c402b-280">添加 `HTTP Strict-Transport-Security` (HSTS) 标头可确保由客户端发起的所有后续请求都通过 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="c402b-280">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="c402b-281">如果以后会禁用 HTTPS，请勿添加 HSTS 头或选择相应的 `max-age`。</span><span class="sxs-lookup"><span data-stu-id="c402b-281">Don't add the HSTS header or chose an appropriate `max-age` if HTTPS will be disabled in the future.</span></span>

<span data-ttu-id="c402b-282">添加 /etc/nginx/proxy.conf 配置文件：</span><span class="sxs-lookup"><span data-stu-id="c402b-282">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="c402b-283">编辑 /etc/nginx/nginx.conf 配置文件。</span><span class="sxs-lookup"><span data-stu-id="c402b-283">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="c402b-284">示例包含一个配置文件中的 `http` 和 `server` 部分。</span><span class="sxs-lookup"><span data-stu-id="c402b-284">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="c402b-285">保护 Nginx 免受点击劫持的侵害</span><span class="sxs-lookup"><span data-stu-id="c402b-285">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="c402b-286">[点击劫持](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)（也称为 *UI 伪装攻击*）是一种恶意攻击，其中网站访问者会上当受骗，从而导致在与当前要访问的页面不同的页面上单击链接或按钮。</span><span class="sxs-lookup"><span data-stu-id="c402b-286">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="c402b-287">使用 `X-FRAME-OPTIONS` 可保护网站。</span><span class="sxs-lookup"><span data-stu-id="c402b-287">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="c402b-288">缓解点击劫持攻击：</span><span class="sxs-lookup"><span data-stu-id="c402b-288">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="c402b-289">编辑 nginx.conf 文件：</span><span class="sxs-lookup"><span data-stu-id="c402b-289">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="c402b-290">添加行 `add_header X-Frame-Options "SAMEORIGIN";`。</span><span class="sxs-lookup"><span data-stu-id="c402b-290">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="c402b-291">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="c402b-291">Save the file.</span></span>
1. <span data-ttu-id="c402b-292">重启 Nginx。</span><span class="sxs-lookup"><span data-stu-id="c402b-292">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="c402b-293">MIME 类型探查</span><span class="sxs-lookup"><span data-stu-id="c402b-293">MIME-type sniffing</span></span>

<span data-ttu-id="c402b-294">此标头可阻止大部分浏览器通过 MIME 方式探查来自已声明内容类型的响应，因为标头会指示浏览器不要替代响应内容类型。</span><span class="sxs-lookup"><span data-stu-id="c402b-294">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="c402b-295">使用 `nosniff` 选项后，如果服务器认为内容是“文本/html”，则浏览器将其显示为“文本/html”。</span><span class="sxs-lookup"><span data-stu-id="c402b-295">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="c402b-296">编辑 nginx.conf 文件：</span><span class="sxs-lookup"><span data-stu-id="c402b-296">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="c402b-297">添加行 `add_header X-Content-Type-Options "nosniff";` 并保存文件，然后重新启动 Nginx。</span><span class="sxs-lookup"><span data-stu-id="c402b-297">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="c402b-298">其他 Nginx 建议</span><span class="sxs-lookup"><span data-stu-id="c402b-298">Additional Nginx suggestions</span></span>

<span data-ttu-id="c402b-299">在服务器上升级共享框架后，重启服务器托管的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c402b-299">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c402b-300">其他资源</span><span class="sxs-lookup"><span data-stu-id="c402b-300">Additional resources</span></span>

* [<span data-ttu-id="c402b-301">Linux 上 .NET Core 的先决条件</span><span class="sxs-lookup"><span data-stu-id="c402b-301">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="c402b-302">Nginx：二进制版本：官方 Debian/Ubuntu 包</span><span class="sxs-lookup"><span data-stu-id="c402b-302">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="c402b-303">NGINX：使用转接头</span><span class="sxs-lookup"><span data-stu-id="c402b-303">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
