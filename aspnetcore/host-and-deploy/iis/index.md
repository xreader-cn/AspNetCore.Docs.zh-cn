---
title: 使用 IIS 在 Windows 上托管 ASP.NET Core
author: rick-anderson
description: 了解如何在 Windows Server Internet Information Services (IIS) 上托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/index
ms.openlocfilehash: c3841babe213a9a3f303b8f9b83a947fd33ad647
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003135"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="c76c2-103">使用 IIS 在 Windows 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c76c2-103">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c76c2-104">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-104">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="c76c2-105">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="c76c2-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="c76c2-106">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="c76c2-106">Supported operating systems</span></span>

<span data-ttu-id="c76c2-107">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="c76c2-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="c76c2-108">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-108">Windows 7 or later</span></span>
* <span data-ttu-id="c76c2-109">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-109">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="c76c2-110">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="c76c2-111">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="c76c2-112">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="c76c2-113">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-113">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="c76c2-114">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="c76c2-114">Supported platforms</span></span>

<span data-ttu-id="c76c2-115">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-115">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="c76c2-116">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="c76c2-116">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="c76c2-117">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="c76c2-117">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="c76c2-118">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="c76c2-118">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="c76c2-119">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="c76c2-119">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="c76c2-120">为 32 位 (x86) 发布的应用必须已为其 IIS 应用程序池启用 32 位。</span><span class="sxs-lookup"><span data-stu-id="c76c2-120">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="c76c2-121">有关详细信息，请参阅[创建 IIS 站点](#create-the-iis-site)部分。</span><span class="sxs-lookup"><span data-stu-id="c76c2-121">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="c76c2-122">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-122">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="c76c2-123">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-123">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="c76c2-124">托管模型</span><span class="sxs-lookup"><span data-stu-id="c76c2-124">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="c76c2-125">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="c76c2-125">In-process hosting model</span></span>

<span data-ttu-id="c76c2-126">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-126">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="c76c2-127">进程内承载相较进程外承载提供更优的性能，因为请求并不通过环回适配器进行代理，环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="c76c2-127">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="c76c2-128">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-128">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c76c2-129">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="c76c2-129">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="c76c2-130">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="c76c2-130">Performs app initialization.</span></span>
  * <span data-ttu-id="c76c2-131">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-131">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="c76c2-132">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-132">Calls `Program.Main`.</span></span>
* <span data-ttu-id="c76c2-133">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="c76c2-133">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="c76c2-134">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="c76c2-134">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="c76c2-136">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-136">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="c76c2-137">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-137">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="c76c2-138">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-138">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="c76c2-139">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="c76c2-139">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="c76c2-140">在 IIS HTTP 服务器处理请求后：</span><span class="sxs-lookup"><span data-stu-id="c76c2-140">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="c76c2-141">请求被发送到 ASP.NET Core 中间件管道。</span><span class="sxs-lookup"><span data-stu-id="c76c2-141">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="c76c2-142">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="c76c2-142">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="c76c2-143">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="c76c2-143">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="c76c2-144">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="c76c2-144">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="c76c2-145">对于现有应用，进程内托管是可选功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-145">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="c76c2-146">ASP.NET Core Web 模板使用进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="c76c2-146">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="c76c2-147">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。  </span><span class="sxs-lookup"><span data-stu-id="c76c2-147">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="c76c2-148">性能测试表明，与在进程外托管应用并将请求代理传入 [Kestrel](xref:fundamentals/servers/kestrel) 相比，在进程中托管 .NET Core 应用可提供明显更高的请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="c76c2-148">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="c76c2-149">作为单个文件可执行文件发布的应用无法由进程内托管模型加载。</span><span class="sxs-lookup"><span data-stu-id="c76c2-149">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="c76c2-150">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="c76c2-150">Out-of-process hosting model</span></span>

<span data-ttu-id="c76c2-151">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-151">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="c76c2-152">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-152">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="c76c2-153">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="c76c2-153">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c76c2-154">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="c76c2-154">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="c76c2-156">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-156">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="c76c2-157">驱动程序将请求路由到网站的配置端口上的 IIS。</span><span class="sxs-lookup"><span data-stu-id="c76c2-157">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="c76c2-158">配置的端口通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-158">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="c76c2-159">此模块将该请求转发到应用的随机端口上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c76c2-159">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="c76c2-160">随机端口不是 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="c76c2-160">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="c76c2-161">ASP.NET Core 模块在启动时通过环境变量指定端口。</span><span class="sxs-lookup"><span data-stu-id="c76c2-161">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="c76c2-162"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-162">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="c76c2-163">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-163">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="c76c2-164">此模块不支持 HTTPS 转发。</span><span class="sxs-lookup"><span data-stu-id="c76c2-164">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="c76c2-165">即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="c76c2-165">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="c76c2-166">Kestrel 从模块获取请求后，请求会被转发到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-166">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c76c2-167">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="c76c2-167">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c76c2-168">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c76c2-168">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="c76c2-169">应用的响应传递回 IIS，IIS 将响应转发回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="c76c2-169">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="c76c2-170">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-170">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c76c2-171">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-171">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="c76c2-172">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="c76c2-172">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="c76c2-173">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="c76c2-173">Enable the IISIntegration components</span></span>

<span data-ttu-id="c76c2-174">在 `CreateHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="c76c2-174">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="c76c2-175">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/generic-host#default-builder-settings>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-175">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="c76c2-176">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="c76c2-176">IIS options</span></span>

<span data-ttu-id="c76c2-177">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="c76c2-177">**In-process hosting model**</span></span>

<span data-ttu-id="c76c2-178">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-178">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="c76c2-179">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="c76c2-179">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="c76c2-180">选项</span><span class="sxs-lookup"><span data-stu-id="c76c2-180">Option</span></span>                         | <span data-ttu-id="c76c2-181">默认</span><span class="sxs-lookup"><span data-stu-id="c76c2-181">Default</span></span> | <span data-ttu-id="c76c2-182">设置</span><span class="sxs-lookup"><span data-stu-id="c76c2-182">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="c76c2-183">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-183">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c76c2-184">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="c76c2-184">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c76c2-185">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-185">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c76c2-186">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-186">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="c76c2-187">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="c76c2-187">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="c76c2-188">`HttpContext.Request` 和 `HttpContext.Response` 是否允许同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="c76c2-188">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="c76c2-189">获取或设置 `HttpRequest` 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="c76c2-189">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="c76c2-190">请注意，IIS 本身有限制 `maxAllowedContentLength`，这一限制将在 `IISServerOptions` 中设置 `MaxRequestBodySize` 之前进行处理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-190">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="c76c2-191">更改 `MaxRequestBodySize` 不会影响 `maxAllowedContentLength`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-191">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="c76c2-192">若要增加 `maxAllowedContentLength`，请在 web.config  中添加一个条目，将 `maxAllowedContentLength` 设置为更高值。</span><span class="sxs-lookup"><span data-stu-id="c76c2-192">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="c76c2-193">有关更多详细信息，请参阅[配置](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-193">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="c76c2-194">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="c76c2-194">**Out-of-process hosting model**</span></span>

<span data-ttu-id="c76c2-195">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-195">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="c76c2-196">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="c76c2-196">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="c76c2-197">选项</span><span class="sxs-lookup"><span data-stu-id="c76c2-197">Option</span></span>                         | <span data-ttu-id="c76c2-198">默认</span><span class="sxs-lookup"><span data-stu-id="c76c2-198">Default</span></span> | <span data-ttu-id="c76c2-199">设置</span><span class="sxs-lookup"><span data-stu-id="c76c2-199">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="c76c2-200">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-200">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c76c2-201">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="c76c2-201">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c76c2-202">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-202">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c76c2-203">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="c76c2-203">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="c76c2-204">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="c76c2-204">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="c76c2-205">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-205">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c76c2-206">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="c76c2-206">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c76c2-207">将 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块配置为转发：</span><span class="sxs-lookup"><span data-stu-id="c76c2-207">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="c76c2-208">方案 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-208">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="c76c2-209">发起请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="c76c2-209">Remote IP address where the request originated.</span></span>

<span data-ttu-id="c76c2-210">[IIS 集成中间件](#enable-the-iisintegration-components)配置转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-210">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="c76c2-211">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-211">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="c76c2-212">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-212">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="c76c2-213">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="c76c2-213">web.config file</span></span>

<span data-ttu-id="c76c2-214">web.config  文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-214">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c76c2-215">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-215">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="c76c2-216">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-216">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="c76c2-217">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="c76c2-217">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="c76c2-218">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)    。</span><span class="sxs-lookup"><span data-stu-id="c76c2-218">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="c76c2-219">如果项目中存在 web.config  文件，则会使用正确的 processPath  和参数  转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="c76c2-219">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="c76c2-220">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-220">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="c76c2-221">web.config  文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="c76c2-221">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="c76c2-222">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="c76c2-222">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="c76c2-223">要防止 Web SDK 转换 web.config  文件，请使用项目文件中的 \<IsTransformWebConfigDisabled>  属性：</span><span class="sxs-lookup"><span data-stu-id="c76c2-223">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="c76c2-224">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath  和参数  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-224">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="c76c2-225">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-225">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="c76c2-226">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="c76c2-226">web.config file location</span></span>

<span data-ttu-id="c76c2-227">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config  文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-227">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="c76c2-228">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="c76c2-228">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="c76c2-229">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-229">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="c76c2-230">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json  、\<assembly>.xml  （XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-230">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="c76c2-231">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-231">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="c76c2-232">如果缺少 web.config  文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-232">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="c76c2-233">部署中必须始终存在 web.config  文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config  文件。**</span><span class="sxs-lookup"><span data-stu-id="c76c2-233">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="c76c2-234">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="c76c2-234">Transform web.config</span></span>

<span data-ttu-id="c76c2-235">如果需要在发布时转换 web.config  ，请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-235">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="c76c2-236">你可能需要在发布时转换 web.config，以便基于配置、配置文件或环境设置环境变量  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-236">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="c76c2-237">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="c76c2-237">IIS configuration</span></span>

<span data-ttu-id="c76c2-238">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="c76c2-238">**Windows Server operating systems**</span></span>

<span data-ttu-id="c76c2-239">启用 Web 服务器 (IIS)  服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="c76c2-239">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="c76c2-240">通过“管理”  菜单或“服务器管理器”  中的链接使用“添加角色和功能”  向导。</span><span class="sxs-lookup"><span data-stu-id="c76c2-240">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="c76c2-241">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-241">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="c76c2-243">在“功能”  步骤后，为 Web 服务器 (IIS) 加载“角色服务”  步骤。</span><span class="sxs-lookup"><span data-stu-id="c76c2-243">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="c76c2-244">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="c76c2-244">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="c76c2-246">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-246">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c76c2-247">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-247">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="c76c2-248">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-248">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c76c2-249">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-249">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c76c2-250">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-250">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c76c2-251">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-251">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c76c2-252">若要启用 WebSocket，请依次展开以下节点：“Web 服务器”   > “应用开发”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-252">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="c76c2-253">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-253">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c76c2-254">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-254">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c76c2-255">继续执行“确认”步骤，安装 Web 服务器角色和服务  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-255">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="c76c2-256">安装 Web 服务器 (IIS)  角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="c76c2-256">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="c76c2-257">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="c76c2-257">**Windows desktop operating systems**</span></span>

<span data-ttu-id="c76c2-258">启用“IIS 管理控制台”  和“万维网服务”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-258">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c76c2-259">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="c76c2-259">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="c76c2-260">打开“Internet Information Services”  节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-260">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="c76c2-261">打开“Web 管理工具”  节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-261">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="c76c2-262">选中“IIS 管理控制台”框  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-262">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="c76c2-263">选中“万维网服务”  框。</span><span class="sxs-lookup"><span data-stu-id="c76c2-263">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c76c2-264">接受“万维网服务”的默认功能，或自定义 IIS 功能  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-264">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="c76c2-265">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-265">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c76c2-266">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-266">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="c76c2-267">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-267">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c76c2-268">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-268">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c76c2-269">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-269">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c76c2-270">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-270">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c76c2-271">若要启用 WebSocket，请依次展开以下节点：“万维网服务”   > “应用开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-271">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="c76c2-272">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-272">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c76c2-273">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-273">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c76c2-274">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="c76c2-274">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="c76c2-276">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="c76c2-276">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="c76c2-277">在托管系统上安装 .NET Core 托管捆绑包  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-277">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="c76c2-278">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-278">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c76c2-279">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-279">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c76c2-280">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="c76c2-280">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="c76c2-281">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-281">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="c76c2-282">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-282">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="c76c2-283">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-283">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="c76c2-284">直接下载（当前版本）</span><span class="sxs-lookup"><span data-stu-id="c76c2-284">Direct download (current version)</span></span>

<span data-ttu-id="c76c2-285">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="c76c2-285">Download the installer using the following link:</span></span>

[<span data-ttu-id="c76c2-286">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="c76c2-286">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="c76c2-287">先前版本的安装程序</span><span class="sxs-lookup"><span data-stu-id="c76c2-287">Earlier versions of the installer</span></span>

<span data-ttu-id="c76c2-288">若要获取先前版本的安装程序：</span><span class="sxs-lookup"><span data-stu-id="c76c2-288">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="c76c2-289">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="c76c2-289">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="c76c2-290">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-290">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="c76c2-291">在“运行应用 - 运行时”  列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-291">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="c76c2-292">使用“托管捆绑包”链接下载安装程序  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-292">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="c76c2-293">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-293">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="c76c2-294">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-294">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="c76c2-295">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="c76c2-295">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="c76c2-296">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-296">Run the installer on the server.</span></span> <span data-ttu-id="c76c2-297">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="c76c2-297">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="c76c2-298">`OPT_NO_ANCM=1` &ndash; 跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="c76c2-298">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="c76c2-299">`OPT_NO_RUNTIME=1` &ndash; 跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-299">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="c76c2-300">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-300">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c76c2-301">`OPT_NO_SHAREDFX=1` &ndash; 跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-301">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="c76c2-302">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-302">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c76c2-303">`OPT_NO_X86=1` &ndash; 跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-303">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="c76c2-304">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="c76c2-304">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="c76c2-305">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-305">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="c76c2-306">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 当共享配置 (applicationHost.config  ) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="c76c2-306">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="c76c2-307">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-307">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="c76c2-308">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-308">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="c76c2-309">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c76c2-309">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="c76c2-310">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="c76c2-310">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="c76c2-311">ASP.NET Core 不采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="c76c2-311">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="c76c2-312">通过安装新的托管捆绑包升级共享框架后，请重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c76c2-312">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="c76c2-313">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-313">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="c76c2-314">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-314">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="c76c2-315">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="c76c2-315">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="c76c2-316">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-316">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="c76c2-317">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="c76c2-317">The preferred method is to use WebPI.</span></span> <span data-ttu-id="c76c2-318">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-318">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="c76c2-319">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="c76c2-319">Create the IIS site</span></span>

1. <span data-ttu-id="c76c2-320">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-320">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="c76c2-321">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="c76c2-321">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="c76c2-322">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-322">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="c76c2-323">在 IIS 管理器中，打开“连接”  面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-323">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="c76c2-324">右键单击“站点”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="c76c2-324">Right-click the **Sites** folder.</span></span> <span data-ttu-id="c76c2-325">选择上下文菜单中的“添加网站”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-325">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="c76c2-326">提供网站名称，并将物理路径设置为应用的部署文件夹   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-326">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="c76c2-327">提供“绑定”  配置，并通过选择“确定”  创建网站：</span><span class="sxs-lookup"><span data-stu-id="c76c2-327">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="c76c2-329">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-329">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="c76c2-330">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="c76c2-330">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="c76c2-331">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="c76c2-331">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="c76c2-332">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="c76c2-332">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="c76c2-333">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="c76c2-333">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c76c2-334">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-334">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="c76c2-335">在服务器节点下，选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-335">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="c76c2-336">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-336">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="c76c2-337">在“编辑应用程序池”  窗口中，将“.NET CLR 版本”  设置为“无托管代码”  ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-337">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="c76c2-339">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-339">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="c76c2-340">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载。</span><span class="sxs-lookup"><span data-stu-id="c76c2-340">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="c76c2-341">将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR)，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-341">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="c76c2-342">将“.NET CLR 版本”  设置为“无托管代码”  是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-342">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="c76c2-343">*ASP.NET Core 2.2 或更高版本*：</span><span class="sxs-lookup"><span data-stu-id="c76c2-343">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="c76c2-344">对于使用 32 位 SDK 发布的 32 位 (x86) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，且该 SDK 使用[进程内托管模型](#in-process-hosting-model)，请为 32 位启用应用程序池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-344">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="c76c2-345">在 IIS 管理器中，导航到“连接”边栏中的“应用程序池”   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-345">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="c76c2-346">选择应用的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-346">Select the app's Application Pool.</span></span> <span data-ttu-id="c76c2-347">在“操作”边栏中，选择，“高级设置”   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-347">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="c76c2-348">将“启用 32 位应用程序”设置为 `True` 。</span><span class="sxs-lookup"><span data-stu-id="c76c2-348">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="c76c2-349">对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-349">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="c76c2-350">在 IIS 管理器中，导航到“连接”边栏中的“应用程序池”   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-350">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="c76c2-351">选择应用的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-351">Select the app's Application Pool.</span></span> <span data-ttu-id="c76c2-352">在“操作”边栏中，选择，“高级设置”   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-352">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="c76c2-353">将“启用 32 位应用程序”设置为 `False` 。</span><span class="sxs-lookup"><span data-stu-id="c76c2-353">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="c76c2-354">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-354">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="c76c2-355">如果将应用池的默认标识（“进程模型” > “标识”）从 ApplicationPoolIdentity 更改为另一标识，请验证新标识拥有所需的权限，可访问应用的文件夹、数据库和其他所需资源    。</span><span class="sxs-lookup"><span data-stu-id="c76c2-355">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="c76c2-356">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-356">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="c76c2-357">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-357">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="c76c2-358">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-358">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="c76c2-359">部署应用</span><span class="sxs-lookup"><span data-stu-id="c76c2-359">Deploy the app</span></span>

<span data-ttu-id="c76c2-360">将应用程序部署到 IIS 物理路径  文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="c76c2-360">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="c76c2-361">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布  文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="c76c2-361">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="c76c2-362">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-362">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="c76c2-363">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-363">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="c76c2-364">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”  对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="c76c2-364">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="c76c2-366">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-366">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="c76c2-367">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-367">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="c76c2-368">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-368">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="c76c2-369">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="c76c2-369">Alternatives to Web Deploy</span></span>

<span data-ttu-id="c76c2-370">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="c76c2-370">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="c76c2-371">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="c76c2-371">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="c76c2-372">浏览网站</span><span class="sxs-lookup"><span data-stu-id="c76c2-372">Browse the website</span></span>

<span data-ttu-id="c76c2-373">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-373">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="c76c2-374">在以下示例中，站点被绑定到端口  `80` 上 `www.mysite.com` 的 IIS 主机名  中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-374">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="c76c2-375">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="c76c2-375">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="c76c2-377">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="c76c2-377">Locked deployment files</span></span>

<span data-ttu-id="c76c2-378">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="c76c2-378">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="c76c2-379">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-379">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="c76c2-380">若要在部署中解除已锁定的文件，请使用以下方法之一  停止应用池：</span><span class="sxs-lookup"><span data-stu-id="c76c2-380">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="c76c2-381">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-381">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="c76c2-382">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-382">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="c76c2-383">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-383">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="c76c2-384">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-384">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="c76c2-385">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-385">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="c76c2-386">使用 PowerShell 删除 app_offline.html  （需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="c76c2-386">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="c76c2-387">数据保护</span><span class="sxs-lookup"><span data-stu-id="c76c2-387">Data protection</span></span>

<span data-ttu-id="c76c2-388">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-388">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="c76c2-389">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-389">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="c76c2-390">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="c76c2-390">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="c76c2-391">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-391">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="c76c2-392">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="c76c2-392">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="c76c2-393">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="c76c2-393">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="c76c2-394">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="c76c2-394">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="c76c2-395">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-395">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="c76c2-396">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-396">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="c76c2-397">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="c76c2-397">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="c76c2-398">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-398">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="c76c2-399">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="c76c2-399">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="c76c2-400">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-400">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="c76c2-401">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="c76c2-401">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="c76c2-402">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="c76c2-402">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="c76c2-403">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="c76c2-403">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="c76c2-404">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="c76c2-404">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="c76c2-405">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="c76c2-405">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="c76c2-406">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="c76c2-406">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="c76c2-407">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-407">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="c76c2-408">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-408">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="c76c2-409">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="c76c2-409">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="c76c2-410">此设置位于应用池“高级设置”下的“进程模型”部分   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-410">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="c76c2-411">将“加载用户配置文件”设置为 `True` 。</span><span class="sxs-lookup"><span data-stu-id="c76c2-411">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="c76c2-412">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="c76c2-412">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="c76c2-413">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-413">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="c76c2-414">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-414">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="c76c2-415">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-415">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="c76c2-416">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-416">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="c76c2-417">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c76c2-417">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="c76c2-418">导航到 %windir%/system32/inetsrv/config  文件夹。</span><span class="sxs-lookup"><span data-stu-id="c76c2-418">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="c76c2-419">打开 applicationHost.config  文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-419">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="c76c2-420">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="c76c2-420">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="c76c2-421">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-421">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="c76c2-422">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="c76c2-422">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="c76c2-423">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-423">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="c76c2-424">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="c76c2-424">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="c76c2-425">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-425">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="c76c2-426">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-426">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="c76c2-427">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="c76c2-427">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="c76c2-428">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="c76c2-428">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="c76c2-429">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-429">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="c76c2-430">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="c76c2-430">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="c76c2-431">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-431">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="c76c2-432">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-432">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="c76c2-433">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="c76c2-433">Virtual Directories</span></span>

<span data-ttu-id="c76c2-434">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-434">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="c76c2-435">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-435">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="c76c2-436">子应用程序</span><span class="sxs-lookup"><span data-stu-id="c76c2-436">Sub-applications</span></span>

<span data-ttu-id="c76c2-437">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-437">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="c76c2-438">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="c76c2-438">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="c76c2-439">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="c76c2-439">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="c76c2-440">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="c76c2-440">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="c76c2-441">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-441">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="c76c2-442">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-442">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="c76c2-443">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-443">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="c76c2-444">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="c76c2-444">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="c76c2-445">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”  响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="c76c2-445">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="c76c2-446">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="c76c2-446">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="c76c2-447">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-447">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="c76c2-448">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-448">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="c76c2-449">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-449">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="c76c2-450">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-450">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="c76c2-451">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。   </span><span class="sxs-lookup"><span data-stu-id="c76c2-451">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="c76c2-452">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-452">Select **OK**.</span></span>

<span data-ttu-id="c76c2-453">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-453">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="c76c2-454">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-454">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="c76c2-455">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="c76c2-455">Configuration of IIS with web.config</span></span>

<span data-ttu-id="c76c2-456">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config  的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="c76c2-456">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="c76c2-457">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="c76c2-457">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="c76c2-458">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config  文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="c76c2-458">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="c76c2-459">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="c76c2-459">For more information, see the following topics:</span></span>

* [<span data-ttu-id="c76c2-460">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="c76c2-460">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="c76c2-461">若要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”  部分。</span><span class="sxs-lookup"><span data-stu-id="c76c2-461">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="c76c2-462">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="c76c2-462">Configuration sections of web.config</span></span>

<span data-ttu-id="c76c2-463">ASP.NET Core 应用不会使用 web.config  中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="c76c2-463">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="c76c2-464">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-464">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="c76c2-465">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-465">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="c76c2-466">应用程序池</span><span class="sxs-lookup"><span data-stu-id="c76c2-466">Application Pools</span></span>

<span data-ttu-id="c76c2-467">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="c76c2-467">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="c76c2-468">进程内托管 &ndash; 应用都需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-468">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="c76c2-469">进程外托管 &ndash; 建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="c76c2-469">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="c76c2-470">IIS“添加网站”对话框默认为每应用一个应用池  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-470">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="c76c2-471">提供了站点名称时，该文本会自动传输到“应用程序池”文本框   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-471">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="c76c2-472">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-472">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="c76c2-473">应用程序池标识</span><span class="sxs-lookup"><span data-stu-id="c76c2-473">Application Pool Identity</span></span>

<span data-ttu-id="c76c2-474">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="c76c2-474">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="c76c2-475">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="c76c2-475">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="c76c2-476">在 IIS 管理控制台中，确保应用池“高级设置”下的“标识”设置为使用 ApplicationPoolIdentity    ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-476">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="c76c2-478">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="c76c2-478">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="c76c2-479">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="c76c2-479">Resources can be secured using this identity.</span></span> <span data-ttu-id="c76c2-480">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="c76c2-480">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="c76c2-481">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="c76c2-481">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="c76c2-482">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="c76c2-482">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="c76c2-483">右键单击该目录，然后选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-483">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="c76c2-484">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮    。</span><span class="sxs-lookup"><span data-stu-id="c76c2-484">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="c76c2-485">选择“位置”按钮，并确保该系统处于选中状态  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-485">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="c76c2-486">在“输入要选择的对象名称”  区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="c76c2-486">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="c76c2-487">选择“检查名称”按钮  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-487">Select the **Check Names** button.</span></span> <span data-ttu-id="c76c2-488">有关 DefaultAppPool  ，请检查使用 IIS AppPool\DefaultAppPool  的名称。</span><span class="sxs-lookup"><span data-stu-id="c76c2-488">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="c76c2-489">当选择“检查名称”  按钮时，对象名称区域中会显示 DefaultAppPool  的值。</span><span class="sxs-lookup"><span data-stu-id="c76c2-489">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="c76c2-490">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="c76c2-490">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="c76c2-491">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="c76c2-491">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="c76c2-493">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-493">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="c76c2-495">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-495">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="c76c2-496">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-496">Provide additional permissions as needed.</span></span>

<span data-ttu-id="c76c2-497">也可使用 ICACLS  工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-497">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="c76c2-498">以 DefaultAppPool  为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="c76c2-498">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="c76c2-499">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="c76c2-499">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c76c2-500">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="c76c2-500">HTTP/2 support</span></span>

<span data-ttu-id="c76c2-501">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="c76c2-501">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="c76c2-502">进程内</span><span class="sxs-lookup"><span data-stu-id="c76c2-502">In-process</span></span>
  * <span data-ttu-id="c76c2-503">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-503">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c76c2-504">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="c76c2-504">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="c76c2-505">进程外</span><span class="sxs-lookup"><span data-stu-id="c76c2-505">Out-of-process</span></span>
  * <span data-ttu-id="c76c2-506">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-506">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c76c2-507">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c76c2-507">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c76c2-508">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="c76c2-508">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c76c2-509">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-509">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="c76c2-510">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-510">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="c76c2-511">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-511">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c76c2-512">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c76c2-512">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c76c2-513">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c76c2-513">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="c76c2-514">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-514">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="c76c2-515">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="c76c2-515">CORS preflight requests</span></span>

<span data-ttu-id="c76c2-516">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-516">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="c76c2-517">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-517">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="c76c2-518">若要了解如何在 web.config  中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-518">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="c76c2-519">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="c76c2-519">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="c76c2-520">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-520">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="c76c2-521">[应用程序初始化模块](#application-initialization-module) &ndash;应用的托管[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)可配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="c76c2-521">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="c76c2-522">[空闲超时](#idle-timeout) &ndash;可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段期间不超时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-522">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="c76c2-523">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="c76c2-523">Application Initialization Module</span></span>

<span data-ttu-id="c76c2-524">适用于托管在进程内和进程外的应用。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-524">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="c76c2-525">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-525">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="c76c2-526">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="c76c2-526">The request triggers the app to start.</span></span> <span data-ttu-id="c76c2-527">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-527">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="c76c2-528">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="c76c2-528">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="c76c2-529">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-529">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="c76c2-530">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="c76c2-530">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="c76c2-531">打开“Internet Information Services”  >“万维网服务”  >“应用程序开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-531">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="c76c2-532">选中“应用程序初始化”  的复选框。</span><span class="sxs-lookup"><span data-stu-id="c76c2-532">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="c76c2-533">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="c76c2-533">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="c76c2-534">打开“添加角色和功能向导”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-534">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="c76c2-535">在“选择角色服务”面板中，打开“应用程序开发”节点   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-535">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="c76c2-536">选中“应用程序初始化”  的复选框。</span><span class="sxs-lookup"><span data-stu-id="c76c2-536">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="c76c2-537">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="c76c2-537">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="c76c2-538">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="c76c2-538">Using IIS Manager:</span></span>

  1. <span data-ttu-id="c76c2-539">在“连接”  面板中选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-539">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="c76c2-540">在列表中右键单击应用的应用池，并选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-540">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="c76c2-541">默认的“启动模式”为“按需”   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-541">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="c76c2-542">将“启动模式”  设置为“始终运行”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-542">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="c76c2-543">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-543">Select **OK**.</span></span>
  1. <span data-ttu-id="c76c2-544">打开“连接”  面板中的“网站”  节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-544">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="c76c2-545">右键单击应用，并选择“管理网站”>“高级设置”   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-545">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="c76c2-546">默认的“预加载已启用”设置为“False”   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-546">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="c76c2-547">将“预加载已启用”  设置为“True”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-547">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="c76c2-548">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-548">Select **OK**.</span></span>

* <span data-ttu-id="c76c2-549">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中   ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-549">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="c76c2-550">空闲超时</span><span class="sxs-lookup"><span data-stu-id="c76c2-550">Idle Timeout</span></span>

<span data-ttu-id="c76c2-551">仅适用于托管在进程内的应用。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-551">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="c76c2-552">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-552">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="c76c2-553">在“连接”  面板中选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-553">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="c76c2-554">在列表中右键单击应用的应用池，并选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-554">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="c76c2-555">默认的“空闲超时(分钟)”为“20”分钟   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-555">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="c76c2-556">将“空闲超时(分钟)”设置为“0”（零）   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-556">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="c76c2-557">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-557">Select **OK**.</span></span>
1. <span data-ttu-id="c76c2-558">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="c76c2-558">Recycle the worker process.</span></span>

<span data-ttu-id="c76c2-559">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="c76c2-559">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="c76c2-560">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="c76c2-560">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="c76c2-561">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-561">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="c76c2-562">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="c76c2-562">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="c76c2-563">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="c76c2-563">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="c76c2-564">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-564">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="c76c2-565">[应用程序池的进程模型设置 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-565">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="c76c2-566">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="c76c2-566">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="c76c2-567">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="c76c2-567">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="c76c2-568">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="c76c2-568">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="c76c2-569">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-569">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="c76c2-570">其他资源</span><span class="sxs-lookup"><span data-stu-id="c76c2-570">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="c76c2-571">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="c76c2-571">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="c76c2-572">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="c76c2-572">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="c76c2-573">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c76c2-573">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c76c2-574">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-574">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="c76c2-575">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="c76c2-575">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="c76c2-576">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="c76c2-576">Supported operating systems</span></span>

<span data-ttu-id="c76c2-577">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="c76c2-577">The following operating systems are supported:</span></span>

* <span data-ttu-id="c76c2-578">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-578">Windows 7 or later</span></span>
* <span data-ttu-id="c76c2-579">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-579">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c76c2-580">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-580">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="c76c2-581">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-581">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="c76c2-582">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-582">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="c76c2-583">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-583">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="c76c2-584">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="c76c2-584">Supported platforms</span></span>

<span data-ttu-id="c76c2-585">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-585">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="c76c2-586">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="c76c2-586">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="c76c2-587">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="c76c2-587">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="c76c2-588">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="c76c2-588">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="c76c2-589">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="c76c2-589">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="c76c2-590">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-590">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="c76c2-591">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-591">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="c76c2-592">托管模型</span><span class="sxs-lookup"><span data-stu-id="c76c2-592">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="c76c2-593">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="c76c2-593">In-process hosting model</span></span>

<span data-ttu-id="c76c2-594">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-594">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="c76c2-595">进程内托管的性能高于进程外托管的性能，因为：</span><span class="sxs-lookup"><span data-stu-id="c76c2-595">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="c76c2-596">请求并不通过环回适配器进行代理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-596">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="c76c2-597">环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="c76c2-597">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="c76c2-598">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-598">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c76c2-599">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="c76c2-599">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="c76c2-600">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="c76c2-600">Performs app initialization.</span></span>
  * <span data-ttu-id="c76c2-601">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-601">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="c76c2-602">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-602">Calls `Program.Main`.</span></span>
* <span data-ttu-id="c76c2-603">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="c76c2-603">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="c76c2-604">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="c76c2-604">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="c76c2-605">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="c76c2-605">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

<span data-ttu-id="c76c2-607">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-607">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="c76c2-608">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-608">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="c76c2-609">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-609">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="c76c2-610">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="c76c2-610">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="c76c2-611">IIS HTTP 服务器处理请求之后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-611">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c76c2-612">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="c76c2-612">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c76c2-613">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="c76c2-613">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="c76c2-614">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="c76c2-614">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="c76c2-615">进程内托管选择使用现有应用，但 [dotnet new](/dotnet/core/tools/dotnet-new) 模板默认使用所有 IIS 和 IIS Express 方案的进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="c76c2-615">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="c76c2-616">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。  </span><span class="sxs-lookup"><span data-stu-id="c76c2-616">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="c76c2-617">性能测试表明，与在进程外托管应用并将请求代理到 [Kestrel](xref:fundamentals/servers/kestrel) 服务器相比，在进程中托管 .NET Core 应用可以大大提升请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="c76c2-617">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="c76c2-618">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="c76c2-618">Out-of-process hosting model</span></span>

<span data-ttu-id="c76c2-619">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-619">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="c76c2-620">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-620">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="c76c2-621">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="c76c2-621">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c76c2-622">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="c76c2-622">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="c76c2-624">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-624">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="c76c2-625">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-625">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="c76c2-626">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c76c2-626">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="c76c2-627">该模块在启动时通过环境变量指定端口，<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-627">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="c76c2-628">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-628">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="c76c2-629">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="c76c2-629">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="c76c2-630">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-630">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c76c2-631">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="c76c2-631">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c76c2-632">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c76c2-632">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="c76c2-633">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="c76c2-633">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="c76c2-634">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-634">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c76c2-635">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-635">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="c76c2-636">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="c76c2-636">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="c76c2-637">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="c76c2-637">Enable the IISIntegration components</span></span>

<span data-ttu-id="c76c2-638">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="c76c2-638">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="c76c2-639">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-639">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="c76c2-640">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="c76c2-640">IIS options</span></span>

<span data-ttu-id="c76c2-641">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="c76c2-641">**In-process hosting model**</span></span>

<span data-ttu-id="c76c2-642">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-642">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="c76c2-643">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="c76c2-643">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="c76c2-644">选项</span><span class="sxs-lookup"><span data-stu-id="c76c2-644">Option</span></span>                         | <span data-ttu-id="c76c2-645">默认</span><span class="sxs-lookup"><span data-stu-id="c76c2-645">Default</span></span> | <span data-ttu-id="c76c2-646">设置</span><span class="sxs-lookup"><span data-stu-id="c76c2-646">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="c76c2-647">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-647">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c76c2-648">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="c76c2-648">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c76c2-649">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-649">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c76c2-650">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-650">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="c76c2-651">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="c76c2-651">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="c76c2-652">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="c76c2-652">**Out-of-process hosting model**</span></span>

<span data-ttu-id="c76c2-653">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-653">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="c76c2-654">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="c76c2-654">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="c76c2-655">选项</span><span class="sxs-lookup"><span data-stu-id="c76c2-655">Option</span></span>                         | <span data-ttu-id="c76c2-656">默认</span><span class="sxs-lookup"><span data-stu-id="c76c2-656">Default</span></span> | <span data-ttu-id="c76c2-657">设置</span><span class="sxs-lookup"><span data-stu-id="c76c2-657">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="c76c2-658">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-658">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c76c2-659">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="c76c2-659">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c76c2-660">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-660">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c76c2-661">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="c76c2-661">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="c76c2-662">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="c76c2-662">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="c76c2-663">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-663">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c76c2-664">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="c76c2-664">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c76c2-665">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="c76c2-665">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="c76c2-666">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-666">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="c76c2-667">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-667">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="c76c2-668">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="c76c2-668">web.config file</span></span>

<span data-ttu-id="c76c2-669">web.config  文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-669">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c76c2-670">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-670">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="c76c2-671">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-671">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="c76c2-672">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="c76c2-672">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="c76c2-673">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)    。</span><span class="sxs-lookup"><span data-stu-id="c76c2-673">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="c76c2-674">如果项目中存在 web.config  文件，则会使用正确的 processPath  和参数  转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="c76c2-674">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="c76c2-675">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-675">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="c76c2-676">web.config  文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="c76c2-676">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="c76c2-677">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="c76c2-677">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="c76c2-678">要防止 Web SDK 转换 web.config  文件，请使用项目文件中的 \<IsTransformWebConfigDisabled>  属性：</span><span class="sxs-lookup"><span data-stu-id="c76c2-678">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="c76c2-679">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath  和参数  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-679">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="c76c2-680">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-680">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="c76c2-681">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="c76c2-681">web.config file location</span></span>

<span data-ttu-id="c76c2-682">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config  文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-682">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="c76c2-683">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="c76c2-683">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="c76c2-684">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-684">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="c76c2-685">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json  、\<assembly>.xml  （XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-685">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="c76c2-686">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-686">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="c76c2-687">如果缺少 web.config  文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-687">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="c76c2-688">部署中必须始终存在 web.config  文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config  文件。**</span><span class="sxs-lookup"><span data-stu-id="c76c2-688">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="c76c2-689">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="c76c2-689">Transform web.config</span></span>

<span data-ttu-id="c76c2-690">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-690">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="c76c2-691">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="c76c2-691">IIS configuration</span></span>

<span data-ttu-id="c76c2-692">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="c76c2-692">**Windows Server operating systems**</span></span>

<span data-ttu-id="c76c2-693">启用 Web 服务器 (IIS)  服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="c76c2-693">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="c76c2-694">通过“管理”  菜单或“服务器管理器”  中的链接使用“添加角色和功能”  向导。</span><span class="sxs-lookup"><span data-stu-id="c76c2-694">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="c76c2-695">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-695">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="c76c2-697">在“功能”  步骤后，为 Web 服务器 (IIS) 加载“角色服务”  步骤。</span><span class="sxs-lookup"><span data-stu-id="c76c2-697">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="c76c2-698">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="c76c2-698">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="c76c2-700">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-700">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c76c2-701">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-701">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="c76c2-702">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-702">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c76c2-703">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-703">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c76c2-704">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-704">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c76c2-705">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-705">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c76c2-706">若要启用 WebSocket，请依次展开以下节点：“Web 服务器”   > “应用开发”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-706">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="c76c2-707">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-707">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c76c2-708">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-708">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c76c2-709">继续执行“确认”步骤，安装 Web 服务器角色和服务  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-709">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="c76c2-710">安装 Web 服务器 (IIS)  角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="c76c2-710">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="c76c2-711">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="c76c2-711">**Windows desktop operating systems**</span></span>

<span data-ttu-id="c76c2-712">启用“IIS 管理控制台”  和“万维网服务”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-712">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c76c2-713">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="c76c2-713">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="c76c2-714">打开“Internet Information Services”  节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-714">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="c76c2-715">打开“Web 管理工具”  节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-715">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="c76c2-716">选中“IIS 管理控制台”框  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-716">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="c76c2-717">选中“万维网服务”  框。</span><span class="sxs-lookup"><span data-stu-id="c76c2-717">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c76c2-718">接受“万维网服务”的默认功能，或自定义 IIS 功能  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-718">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="c76c2-719">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-719">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c76c2-720">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-720">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="c76c2-721">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-721">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c76c2-722">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-722">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c76c2-723">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-723">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c76c2-724">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-724">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c76c2-725">若要启用 WebSocket，请依次展开以下节点：“万维网服务”   > “应用开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-725">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="c76c2-726">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-726">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c76c2-727">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-727">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c76c2-728">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="c76c2-728">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="c76c2-730">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="c76c2-730">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="c76c2-731">在托管系统上安装 .NET Core 托管捆绑包  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-731">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="c76c2-732">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-732">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c76c2-733">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-733">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c76c2-734">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="c76c2-734">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="c76c2-735">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-735">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="c76c2-736">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-736">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="c76c2-737">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-737">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="c76c2-738">下载</span><span class="sxs-lookup"><span data-stu-id="c76c2-738">Download</span></span>

1. <span data-ttu-id="c76c2-739">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="c76c2-739">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="c76c2-740">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-740">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="c76c2-741">在“运行应用 - 运行时”  列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-741">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="c76c2-742">使用“托管捆绑包”链接下载安装程序  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-742">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="c76c2-743">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-743">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="c76c2-744">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-744">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="c76c2-745">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="c76c2-745">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="c76c2-746">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-746">Run the installer on the server.</span></span> <span data-ttu-id="c76c2-747">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="c76c2-747">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="c76c2-748">`OPT_NO_ANCM=1` &ndash; 跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="c76c2-748">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="c76c2-749">`OPT_NO_RUNTIME=1` &ndash; 跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-749">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="c76c2-750">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-750">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c76c2-751">`OPT_NO_SHAREDFX=1` &ndash; 跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-751">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="c76c2-752">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-752">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c76c2-753">`OPT_NO_X86=1` &ndash; 跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-753">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="c76c2-754">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="c76c2-754">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="c76c2-755">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-755">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="c76c2-756">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 当共享配置 (applicationHost.config  ) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="c76c2-756">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="c76c2-757">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-757">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="c76c2-758">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-758">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="c76c2-759">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c76c2-759">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="c76c2-760">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="c76c2-760">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="c76c2-761">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-761">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="c76c2-762">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="c76c2-762">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="c76c2-763">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="c76c2-763">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="c76c2-764">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="c76c2-764">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="c76c2-765">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="c76c2-765">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="c76c2-766">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="c76c2-766">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="c76c2-767">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-767">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="c76c2-768">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-768">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="c76c2-769">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="c76c2-769">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="c76c2-770">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-770">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="c76c2-771">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="c76c2-771">The preferred method is to use WebPI.</span></span> <span data-ttu-id="c76c2-772">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-772">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="c76c2-773">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="c76c2-773">Create the IIS site</span></span>

1. <span data-ttu-id="c76c2-774">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-774">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="c76c2-775">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="c76c2-775">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="c76c2-776">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-776">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="c76c2-777">在 IIS 管理器中，打开“连接”  面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-777">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="c76c2-778">右键单击“站点”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="c76c2-778">Right-click the **Sites** folder.</span></span> <span data-ttu-id="c76c2-779">选择上下文菜单中的“添加网站”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-779">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="c76c2-780">提供网站名称，并将物理路径设置为应用的部署文件夹   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-780">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="c76c2-781">提供“绑定”  配置，并通过选择“确定”  创建网站：</span><span class="sxs-lookup"><span data-stu-id="c76c2-781">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="c76c2-783">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-783">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="c76c2-784">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="c76c2-784">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="c76c2-785">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="c76c2-785">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="c76c2-786">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="c76c2-786">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="c76c2-787">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="c76c2-787">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c76c2-788">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-788">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="c76c2-789">在服务器节点下，选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-789">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="c76c2-790">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-790">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="c76c2-791">在“编辑应用程序池”  窗口中，将“.NET CLR 版本”  设置为“无托管代码”  ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-791">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="c76c2-793">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-793">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="c76c2-794">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-794">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="c76c2-795">将“.NET CLR 版本”  设置为“无托管代码”  是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-795">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="c76c2-796">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-796">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="c76c2-797">在 IIS 管理器 >“应用程序池”  的“操作”  侧栏中，选择“设置应用程序池默认设置”  或“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-797">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="c76c2-798">找到“启用 32 位应用程序”并将值设置为 `False` 。</span><span class="sxs-lookup"><span data-stu-id="c76c2-798">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="c76c2-799">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-799">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="c76c2-800">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-800">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="c76c2-801">如果将应用池的默认标识（“进程模型” > “标识”）从 ApplicationPoolIdentity 更改为另一标识，请验证新标识拥有所需的权限，可访问应用的文件夹、数据库和其他所需资源    。</span><span class="sxs-lookup"><span data-stu-id="c76c2-801">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="c76c2-802">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-802">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="c76c2-803">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-803">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="c76c2-804">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-804">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="c76c2-805">部署应用</span><span class="sxs-lookup"><span data-stu-id="c76c2-805">Deploy the app</span></span>

<span data-ttu-id="c76c2-806">将应用程序部署到 IIS 物理路径  文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="c76c2-806">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="c76c2-807">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布  文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="c76c2-807">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="c76c2-808">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-808">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="c76c2-809">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-809">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="c76c2-810">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”  对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="c76c2-810">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="c76c2-812">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-812">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="c76c2-813">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-813">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="c76c2-814">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-814">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="c76c2-815">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="c76c2-815">Alternatives to Web Deploy</span></span>

<span data-ttu-id="c76c2-816">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="c76c2-816">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="c76c2-817">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="c76c2-817">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="c76c2-818">浏览网站</span><span class="sxs-lookup"><span data-stu-id="c76c2-818">Browse the website</span></span>

<span data-ttu-id="c76c2-819">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-819">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="c76c2-820">在以下示例中，站点被绑定到端口  `80` 上 `www.mysite.com` 的 IIS 主机名  中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-820">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="c76c2-821">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="c76c2-821">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="c76c2-823">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="c76c2-823">Locked deployment files</span></span>

<span data-ttu-id="c76c2-824">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="c76c2-824">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="c76c2-825">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-825">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="c76c2-826">若要在部署中解除已锁定的文件，请使用以下方法之一  停止应用池：</span><span class="sxs-lookup"><span data-stu-id="c76c2-826">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="c76c2-827">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-827">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="c76c2-828">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-828">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="c76c2-829">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-829">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="c76c2-830">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-830">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="c76c2-831">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-831">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="c76c2-832">使用 PowerShell 删除 app_offline.html  （需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="c76c2-832">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="c76c2-833">数据保护</span><span class="sxs-lookup"><span data-stu-id="c76c2-833">Data protection</span></span>

<span data-ttu-id="c76c2-834">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-834">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="c76c2-835">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-835">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="c76c2-836">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="c76c2-836">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="c76c2-837">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-837">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="c76c2-838">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="c76c2-838">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="c76c2-839">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="c76c2-839">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="c76c2-840">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="c76c2-840">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="c76c2-841">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-841">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="c76c2-842">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-842">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="c76c2-843">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="c76c2-843">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="c76c2-844">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-844">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="c76c2-845">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="c76c2-845">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="c76c2-846">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-846">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="c76c2-847">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="c76c2-847">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="c76c2-848">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="c76c2-848">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="c76c2-849">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="c76c2-849">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="c76c2-850">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="c76c2-850">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="c76c2-851">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="c76c2-851">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="c76c2-852">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="c76c2-852">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="c76c2-853">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-853">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="c76c2-854">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-854">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="c76c2-855">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="c76c2-855">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="c76c2-856">此设置位于应用池“高级设置”下的“进程模型”部分   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-856">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="c76c2-857">将“加载用户配置文件”设置为 `True` 。</span><span class="sxs-lookup"><span data-stu-id="c76c2-857">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="c76c2-858">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="c76c2-858">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="c76c2-859">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-859">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="c76c2-860">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-860">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="c76c2-861">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-861">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="c76c2-862">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-862">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="c76c2-863">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c76c2-863">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="c76c2-864">导航到 %windir%/system32/inetsrv/config  文件夹。</span><span class="sxs-lookup"><span data-stu-id="c76c2-864">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="c76c2-865">打开 applicationHost.config  文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-865">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="c76c2-866">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="c76c2-866">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="c76c2-867">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-867">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="c76c2-868">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="c76c2-868">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="c76c2-869">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-869">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="c76c2-870">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="c76c2-870">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="c76c2-871">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-871">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="c76c2-872">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-872">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="c76c2-873">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="c76c2-873">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="c76c2-874">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="c76c2-874">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="c76c2-875">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-875">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="c76c2-876">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="c76c2-876">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="c76c2-877">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-877">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="c76c2-878">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-878">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="c76c2-879">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="c76c2-879">Virtual Directories</span></span>

<span data-ttu-id="c76c2-880">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-880">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="c76c2-881">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-881">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="c76c2-882">子应用程序</span><span class="sxs-lookup"><span data-stu-id="c76c2-882">Sub-applications</span></span>

<span data-ttu-id="c76c2-883">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-883">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="c76c2-884">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="c76c2-884">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="c76c2-885">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="c76c2-885">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="c76c2-886">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="c76c2-886">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="c76c2-887">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-887">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="c76c2-888">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-888">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="c76c2-889">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-889">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="c76c2-890">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="c76c2-890">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="c76c2-891">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”  响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="c76c2-891">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="c76c2-892">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="c76c2-892">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="c76c2-893">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-893">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="c76c2-894">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-894">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="c76c2-895">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-895">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="c76c2-896">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-896">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="c76c2-897">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。   </span><span class="sxs-lookup"><span data-stu-id="c76c2-897">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="c76c2-898">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-898">Select **OK**.</span></span>

<span data-ttu-id="c76c2-899">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-899">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="c76c2-900">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-900">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="c76c2-901">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="c76c2-901">Configuration of IIS with web.config</span></span>

<span data-ttu-id="c76c2-902">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config  的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="c76c2-902">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="c76c2-903">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="c76c2-903">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="c76c2-904">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config  文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="c76c2-904">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="c76c2-905">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="c76c2-905">For more information, see the following topics:</span></span>

* [<span data-ttu-id="c76c2-906">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="c76c2-906">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="c76c2-907">若要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”  部分。</span><span class="sxs-lookup"><span data-stu-id="c76c2-907">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="c76c2-908">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="c76c2-908">Configuration sections of web.config</span></span>

<span data-ttu-id="c76c2-909">ASP.NET Core 应用不会使用 web.config  中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="c76c2-909">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="c76c2-910">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-910">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="c76c2-911">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-911">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="c76c2-912">应用程序池</span><span class="sxs-lookup"><span data-stu-id="c76c2-912">Application Pools</span></span>

<span data-ttu-id="c76c2-913">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="c76c2-913">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="c76c2-914">进程内托管 &ndash; 应用都需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-914">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="c76c2-915">进程外托管 &ndash; 建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="c76c2-915">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="c76c2-916">IIS“添加网站”对话框默认为每应用一个应用池  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-916">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="c76c2-917">提供了站点名称时，该文本会自动传输到“应用程序池”文本框   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-917">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="c76c2-918">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-918">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="c76c2-919">应用程序池标识</span><span class="sxs-lookup"><span data-stu-id="c76c2-919">Application Pool Identity</span></span>

<span data-ttu-id="c76c2-920">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="c76c2-920">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="c76c2-921">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="c76c2-921">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="c76c2-922">在 IIS 管理控制台中，确保应用池“高级设置”下的“标识”设置为使用 ApplicationPoolIdentity    ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-922">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="c76c2-924">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="c76c2-924">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="c76c2-925">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="c76c2-925">Resources can be secured using this identity.</span></span> <span data-ttu-id="c76c2-926">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="c76c2-926">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="c76c2-927">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="c76c2-927">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="c76c2-928">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="c76c2-928">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="c76c2-929">右键单击该目录，然后选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-929">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="c76c2-930">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮    。</span><span class="sxs-lookup"><span data-stu-id="c76c2-930">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="c76c2-931">选择“位置”按钮，并确保该系统处于选中状态  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-931">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="c76c2-932">在“输入要选择的对象名称”  区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="c76c2-932">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="c76c2-933">选择“检查名称”按钮  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-933">Select the **Check Names** button.</span></span> <span data-ttu-id="c76c2-934">有关 DefaultAppPool  ，请检查使用 IIS AppPool\DefaultAppPool  的名称。</span><span class="sxs-lookup"><span data-stu-id="c76c2-934">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="c76c2-935">当选择“检查名称”  按钮时，对象名称区域中会显示 DefaultAppPool  的值。</span><span class="sxs-lookup"><span data-stu-id="c76c2-935">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="c76c2-936">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="c76c2-936">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="c76c2-937">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="c76c2-937">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="c76c2-939">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-939">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="c76c2-941">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-941">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="c76c2-942">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-942">Provide additional permissions as needed.</span></span>

<span data-ttu-id="c76c2-943">也可使用 ICACLS  工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-943">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="c76c2-944">以 DefaultAppPool  为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="c76c2-944">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="c76c2-945">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="c76c2-945">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c76c2-946">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="c76c2-946">HTTP/2 support</span></span>

<span data-ttu-id="c76c2-947">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="c76c2-947">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="c76c2-948">进程内</span><span class="sxs-lookup"><span data-stu-id="c76c2-948">In-process</span></span>
  * <span data-ttu-id="c76c2-949">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-949">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c76c2-950">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="c76c2-950">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="c76c2-951">进程外</span><span class="sxs-lookup"><span data-stu-id="c76c2-951">Out-of-process</span></span>
  * <span data-ttu-id="c76c2-952">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-952">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="c76c2-953">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c76c2-953">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="c76c2-954">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="c76c2-954">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c76c2-955">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-955">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="c76c2-956">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-956">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="c76c2-957">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-957">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c76c2-958">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c76c2-958">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c76c2-959">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c76c2-959">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="c76c2-960">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-960">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="c76c2-961">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="c76c2-961">CORS preflight requests</span></span>

<span data-ttu-id="c76c2-962">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-962">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="c76c2-963">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-963">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="c76c2-964">若要了解如何在 web.config  中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-964">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="c76c2-965">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="c76c2-965">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="c76c2-966">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-966">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="c76c2-967">[应用程序初始化模块](#application-initialization-module) &ndash;应用的托管[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)可配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="c76c2-967">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="c76c2-968">[空闲超时](#idle-timeout) &ndash;可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段期间不超时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-968">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="c76c2-969">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="c76c2-969">Application Initialization Module</span></span>

<span data-ttu-id="c76c2-970">适用于托管在进程内和进程外的应用。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-970">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="c76c2-971">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-971">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="c76c2-972">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="c76c2-972">The request triggers the app to start.</span></span> <span data-ttu-id="c76c2-973">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-973">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="c76c2-974">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="c76c2-974">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="c76c2-975">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-975">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="c76c2-976">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="c76c2-976">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="c76c2-977">打开“Internet Information Services”  >“万维网服务”  >“应用程序开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-977">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="c76c2-978">选中“应用程序初始化”  的复选框。</span><span class="sxs-lookup"><span data-stu-id="c76c2-978">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="c76c2-979">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="c76c2-979">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="c76c2-980">打开“添加角色和功能向导”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-980">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="c76c2-981">在“选择角色服务”面板中，打开“应用程序开发”节点   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-981">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="c76c2-982">选中“应用程序初始化”  的复选框。</span><span class="sxs-lookup"><span data-stu-id="c76c2-982">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="c76c2-983">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="c76c2-983">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="c76c2-984">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="c76c2-984">Using IIS Manager:</span></span>

  1. <span data-ttu-id="c76c2-985">在“连接”  面板中选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-985">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="c76c2-986">在列表中右键单击应用的应用池，并选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-986">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="c76c2-987">默认的“启动模式”为“按需”   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-987">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="c76c2-988">将“启动模式”  设置为“始终运行”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-988">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="c76c2-989">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-989">Select **OK**.</span></span>
  1. <span data-ttu-id="c76c2-990">打开“连接”  面板中的“网站”  节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-990">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="c76c2-991">右键单击应用，并选择“管理网站”>“高级设置”   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-991">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="c76c2-992">默认的“预加载已启用”设置为“False”   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-992">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="c76c2-993">将“预加载已启用”  设置为“True”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-993">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="c76c2-994">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-994">Select **OK**.</span></span>

* <span data-ttu-id="c76c2-995">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中   ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-995">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="c76c2-996">空闲超时</span><span class="sxs-lookup"><span data-stu-id="c76c2-996">Idle Timeout</span></span>

<span data-ttu-id="c76c2-997">仅适用于托管在进程内的应用。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-997">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="c76c2-998">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-998">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="c76c2-999">在“连接”  面板中选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-999">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="c76c2-1000">在列表中右键单击应用的应用池，并选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1000">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="c76c2-1001">默认的“空闲超时(分钟)”为“20”分钟   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1001">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="c76c2-1002">将“空闲超时(分钟)”设置为“0”（零）   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1002">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="c76c2-1003">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1003">Select **OK**.</span></span>
1. <span data-ttu-id="c76c2-1004">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1004">Recycle the worker process.</span></span>

<span data-ttu-id="c76c2-1005">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1005">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="c76c2-1006">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1006">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="c76c2-1007">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1007">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="c76c2-1008">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="c76c2-1008">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="c76c2-1009">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="c76c2-1009">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="c76c2-1010">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1010">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="c76c2-1011">[应用程序池的进程模型设置 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1011">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="c76c2-1012">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="c76c2-1012">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="c76c2-1013">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="c76c2-1013">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="c76c2-1014">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="c76c2-1014">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="c76c2-1015">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-1015">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="c76c2-1016">其他资源</span><span class="sxs-lookup"><span data-stu-id="c76c2-1016">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="c76c2-1017">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="c76c2-1017">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="c76c2-1018">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="c76c2-1018">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="c76c2-1019">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c76c2-1019">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c76c2-1020">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1020">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="c76c2-1021">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="c76c2-1021">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="c76c2-1022">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="c76c2-1022">Supported operating systems</span></span>

<span data-ttu-id="c76c2-1023">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1023">The following operating systems are supported:</span></span>

* <span data-ttu-id="c76c2-1024">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-1024">Windows 7 or later</span></span>
* <span data-ttu-id="c76c2-1025">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-1025">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c76c2-1026">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1026">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="c76c2-1027">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1027">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="c76c2-1028">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1028">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="c76c2-1029">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1029">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="c76c2-1030">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="c76c2-1030">Supported platforms</span></span>

<span data-ttu-id="c76c2-1031">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1031">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="c76c2-1032">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1032">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="c76c2-1033">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1033">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="c76c2-1034">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1034">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="c76c2-1035">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1035">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="c76c2-1036">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1036">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="c76c2-1037">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1037">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="c76c2-1038">ASP.NET Core 随附有 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，后者是默认的跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1038">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="c76c2-1039">使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用在独立于 IIS 工作进程（进程外）  和 [Kestrel 服务器](xref:fundamentals/servers/index#kestrel)的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1039">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="c76c2-1040">由于 ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，因此该模块会处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1040">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="c76c2-1041">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1041">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="c76c2-1042">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1042">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c76c2-1043">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1043">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="c76c2-1045">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1045">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="c76c2-1046">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1046">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="c76c2-1047">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1047">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="c76c2-1048">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1048">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="c76c2-1049">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1049">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="c76c2-1050">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1050">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="c76c2-1051">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1051">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c76c2-1052">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1052">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c76c2-1053">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1053">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="c76c2-1054">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1054">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="c76c2-1055">`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的基础路径和端口来启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1055">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="c76c2-1056">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1056">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="c76c2-1057">`CreateDefaultBuilder` 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 方法。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1057">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="c76c2-1058">`UseIISIntegration` 配置在 localhost IP 地址 (`127.0.0.1`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1058">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="c76c2-1059">如果动态端口为 1234，则 Kestrel 在 `127.0.0.1:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1059">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="c76c2-1060">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1060">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="c76c2-1061">Kestrel 的侦听 API</span><span class="sxs-lookup"><span data-stu-id="c76c2-1061">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="c76c2-1062">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="c76c2-1062">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="c76c2-1063">使用模块时，不需要调用 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1063">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="c76c2-1064">如果调用 `UseUrls` 或 `Listen`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1064">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="c76c2-1065">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1065">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="c76c2-1066">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1066">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="c76c2-1067">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="c76c2-1067">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="c76c2-1068">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="c76c2-1068">Enable the IISIntegration components</span></span>

<span data-ttu-id="c76c2-1069">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1069">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="c76c2-1070">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1070">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="c76c2-1071">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="c76c2-1071">IIS options</span></span>

| <span data-ttu-id="c76c2-1072">选项</span><span class="sxs-lookup"><span data-stu-id="c76c2-1072">Option</span></span>                         | <span data-ttu-id="c76c2-1073">默认</span><span class="sxs-lookup"><span data-stu-id="c76c2-1073">Default</span></span> | <span data-ttu-id="c76c2-1074">设置</span><span class="sxs-lookup"><span data-stu-id="c76c2-1074">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="c76c2-1075">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1075">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c76c2-1076">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1076">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c76c2-1077">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1077">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c76c2-1078">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1078">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="c76c2-1079">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1079">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="c76c2-1080">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1080">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="c76c2-1081">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1081">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="c76c2-1082">选项</span><span class="sxs-lookup"><span data-stu-id="c76c2-1082">Option</span></span>                         | <span data-ttu-id="c76c2-1083">默认</span><span class="sxs-lookup"><span data-stu-id="c76c2-1083">Default</span></span> | <span data-ttu-id="c76c2-1084">设置</span><span class="sxs-lookup"><span data-stu-id="c76c2-1084">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="c76c2-1085">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1085">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="c76c2-1086">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1086">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="c76c2-1087">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1087">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="c76c2-1088">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1088">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="c76c2-1089">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1089">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="c76c2-1090">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1090">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c76c2-1091">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="c76c2-1091">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c76c2-1092">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1092">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="c76c2-1093">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1093">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="c76c2-1094">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1094">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="c76c2-1095">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="c76c2-1095">web.config file</span></span>

<span data-ttu-id="c76c2-1096">web.config  文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1096">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c76c2-1097">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1097">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="c76c2-1098">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1098">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="c76c2-1099">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1099">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="c76c2-1100">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)    。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1100">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="c76c2-1101">如果项目中存在 web.config  文件，则会使用正确的 processPath  和参数  转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1101">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="c76c2-1102">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1102">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="c76c2-1103">web.config  文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1103">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="c76c2-1104">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1104">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="c76c2-1105">要防止 Web SDK 转换 web.config  文件，请使用项目文件中的 \<IsTransformWebConfigDisabled>  属性：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1105">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="c76c2-1106">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath  和参数  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1106">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="c76c2-1107">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1107">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="c76c2-1108">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="c76c2-1108">web.config file location</span></span>

<span data-ttu-id="c76c2-1109">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config  文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1109">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="c76c2-1110">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1110">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="c76c2-1111">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1111">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="c76c2-1112">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json  、\<assembly>.xml  （XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1112">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="c76c2-1113">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1113">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="c76c2-1114">如果缺少 web.config  文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1114">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="c76c2-1115">部署中必须始终存在 web.config  文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config  文件。**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1115">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="c76c2-1116">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="c76c2-1116">Transform web.config</span></span>

<span data-ttu-id="c76c2-1117">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1117">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="c76c2-1118">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="c76c2-1118">IIS configuration</span></span>

<span data-ttu-id="c76c2-1119">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1119">**Windows Server operating systems**</span></span>

<span data-ttu-id="c76c2-1120">启用 Web 服务器 (IIS)  服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1120">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="c76c2-1121">通过“管理”  菜单或“服务器管理器”  中的链接使用“添加角色和功能”  向导。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1121">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="c76c2-1122">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1122">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="c76c2-1124">在“功能”  步骤后，为 Web 服务器 (IIS) 加载“角色服务”  步骤。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1124">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="c76c2-1125">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1125">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="c76c2-1127">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1127">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c76c2-1128">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1128">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="c76c2-1129">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1129">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c76c2-1130">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1130">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c76c2-1131">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1131">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c76c2-1132">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1132">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c76c2-1133">若要启用 WebSocket，请依次展开以下节点：“Web 服务器”   > “应用开发”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1133">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="c76c2-1134">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1134">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c76c2-1135">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1135">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c76c2-1136">继续执行“确认”步骤，安装 Web 服务器角色和服务  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1136">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="c76c2-1137">安装 Web 服务器 (IIS)  角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1137">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="c76c2-1138">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1138">**Windows desktop operating systems**</span></span>

<span data-ttu-id="c76c2-1139">启用“IIS 管理控制台”  和“万维网服务”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1139">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c76c2-1140">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1140">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="c76c2-1141">打开“Internet Information Services”  节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1141">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="c76c2-1142">打开“Web 管理工具”  节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1142">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="c76c2-1143">选中“IIS 管理控制台”框  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1143">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="c76c2-1144">选中“万维网服务”  框。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1144">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="c76c2-1145">接受“万维网服务”的默认功能，或自定义 IIS 功能  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1145">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="c76c2-1146">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1146">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="c76c2-1147">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1147">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="c76c2-1148">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1148">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="c76c2-1149">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1149">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="c76c2-1150">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1150">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="c76c2-1151">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1151">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="c76c2-1152">若要启用 WebSocket，请依次展开以下节点：“万维网服务”   > “应用开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1152">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="c76c2-1153">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1153">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="c76c2-1154">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1154">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="c76c2-1155">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1155">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="c76c2-1157">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="c76c2-1157">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="c76c2-1158">在托管系统上安装 .NET Core 托管捆绑包  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1158">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="c76c2-1159">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1159">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="c76c2-1160">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1160">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c76c2-1161">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1161">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="c76c2-1162">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1162">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="c76c2-1163">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1163">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="c76c2-1164">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1164">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="c76c2-1165">下载</span><span class="sxs-lookup"><span data-stu-id="c76c2-1165">Download</span></span>

1. <span data-ttu-id="c76c2-1166">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1166">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="c76c2-1167">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1167">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="c76c2-1168">在“运行应用 - 运行时”  列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1168">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="c76c2-1169">使用“托管捆绑包”链接下载安装程序  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1169">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="c76c2-1170">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1170">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="c76c2-1171">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1171">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="c76c2-1172">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="c76c2-1172">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="c76c2-1173">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1173">Run the installer on the server.</span></span> <span data-ttu-id="c76c2-1174">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1174">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="c76c2-1175">`OPT_NO_ANCM=1` &ndash; 跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1175">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="c76c2-1176">`OPT_NO_RUNTIME=1` &ndash; 跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1176">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="c76c2-1177">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1177">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c76c2-1178">`OPT_NO_SHAREDFX=1` &ndash; 跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1178">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="c76c2-1179">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1179">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="c76c2-1180">`OPT_NO_X86=1` &ndash; 跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1180">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="c76c2-1181">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1181">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="c76c2-1182">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1182">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="c76c2-1183">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 当共享配置 (applicationHost.config  ) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1183">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="c76c2-1184">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-1184">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="c76c2-1185">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1185">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="c76c2-1186">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1186">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="c76c2-1187">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1187">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="c76c2-1188">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1188">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="c76c2-1189">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1189">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="c76c2-1190">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1190">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="c76c2-1191">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1191">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="c76c2-1192">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1192">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="c76c2-1193">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1193">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="c76c2-1194">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1194">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="c76c2-1195">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-1195">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="c76c2-1196">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1196">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="c76c2-1197">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1197">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="c76c2-1198">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1198">The preferred method is to use WebPI.</span></span> <span data-ttu-id="c76c2-1199">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1199">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="c76c2-1200">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="c76c2-1200">Create the IIS site</span></span>

1. <span data-ttu-id="c76c2-1201">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1201">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="c76c2-1202">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1202">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="c76c2-1203">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1203">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="c76c2-1204">在 IIS 管理器中，打开“连接”  面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1204">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="c76c2-1205">右键单击“站点”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1205">Right-click the **Sites** folder.</span></span> <span data-ttu-id="c76c2-1206">选择上下文菜单中的“添加网站”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1206">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="c76c2-1207">提供网站名称，并将物理路径设置为应用的部署文件夹   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1207">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="c76c2-1208">提供“绑定”  配置，并通过选择“确定”  创建网站：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1208">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="c76c2-1210">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1210">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="c76c2-1211">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1211">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="c76c2-1212">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1212">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="c76c2-1213">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1213">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="c76c2-1214">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1214">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c76c2-1215">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1215">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="c76c2-1216">在服务器节点下，选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1216">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="c76c2-1217">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1217">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="c76c2-1218">在“编辑应用程序池”  窗口中，将“.NET CLR 版本”  设置为“无托管代码”  ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1218">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="c76c2-1220">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1220">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="c76c2-1221">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1221">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="c76c2-1222">将“.NET CLR 版本”  设置为“无托管代码”  是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1222">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="c76c2-1223">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1223">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="c76c2-1224">在 IIS 管理器 >“应用程序池”  的“操作”  侧栏中，选择“设置应用程序池默认设置”  或“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1224">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="c76c2-1225">找到“启用 32 位应用程序”并将值设置为 `False` 。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1225">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="c76c2-1226">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1226">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="c76c2-1227">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1227">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="c76c2-1228">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限    。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1228">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="c76c2-1229">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1229">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="c76c2-1230">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1230">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="c76c2-1231">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1231">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="c76c2-1232">部署应用</span><span class="sxs-lookup"><span data-stu-id="c76c2-1232">Deploy the app</span></span>

<span data-ttu-id="c76c2-1233">将应用程序部署到 IIS 物理路径  文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1233">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="c76c2-1234">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布  文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1234">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="c76c2-1235">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-1235">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="c76c2-1236">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1236">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="c76c2-1237">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”  对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1237">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="c76c2-1239">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-1239">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="c76c2-1240">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1240">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="c76c2-1241">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1241">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="c76c2-1242">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="c76c2-1242">Alternatives to Web Deploy</span></span>

<span data-ttu-id="c76c2-1243">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1243">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="c76c2-1244">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1244">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="c76c2-1245">浏览网站</span><span class="sxs-lookup"><span data-stu-id="c76c2-1245">Browse the website</span></span>

<span data-ttu-id="c76c2-1246">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1246">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="c76c2-1247">在以下示例中，站点被绑定到端口  `80` 上 `www.mysite.com` 的 IIS 主机名  中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1247">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="c76c2-1248">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1248">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="c76c2-1250">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="c76c2-1250">Locked deployment files</span></span>

<span data-ttu-id="c76c2-1251">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1251">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="c76c2-1252">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1252">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="c76c2-1253">若要在部署中解除已锁定的文件，请使用以下方法之一  停止应用池：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1253">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="c76c2-1254">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1254">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="c76c2-1255">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1255">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="c76c2-1256">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1256">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="c76c2-1257">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1257">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="c76c2-1258">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1258">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="c76c2-1259">使用 PowerShell 删除 app_offline.html  （需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1259">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="c76c2-1260">数据保护</span><span class="sxs-lookup"><span data-stu-id="c76c2-1260">Data protection</span></span>

<span data-ttu-id="c76c2-1261">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1261">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="c76c2-1262">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1262">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="c76c2-1263">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1263">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="c76c2-1264">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1264">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="c76c2-1265">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1265">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="c76c2-1266">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1266">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="c76c2-1267">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1267">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="c76c2-1268">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1268">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="c76c2-1269">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1269">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="c76c2-1270">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1270">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="c76c2-1271">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1271">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="c76c2-1272">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1272">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="c76c2-1273">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1273">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="c76c2-1274">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1274">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="c76c2-1275">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1275">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="c76c2-1276">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1276">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="c76c2-1277">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1277">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="c76c2-1278">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1278">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="c76c2-1279">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1279">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="c76c2-1280">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1280">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="c76c2-1281">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1281">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="c76c2-1282">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1282">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="c76c2-1283">此设置位于应用池“高级设置”下的“进程模型”部分   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1283">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="c76c2-1284">将“加载用户配置文件”设置为 `True` 。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1284">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="c76c2-1285">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1285">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="c76c2-1286">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1286">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="c76c2-1287">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1287">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="c76c2-1288">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1288">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="c76c2-1289">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1289">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="c76c2-1290">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1290">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="c76c2-1291">导航到 %windir%/system32/inetsrv/config  文件夹。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1291">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="c76c2-1292">打开 applicationHost.config  文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1292">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="c76c2-1293">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1293">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="c76c2-1294">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1294">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="c76c2-1295">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1295">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="c76c2-1296">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1296">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="c76c2-1297">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1297">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="c76c2-1298">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1298">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="c76c2-1299">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1299">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="c76c2-1300">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1300">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="c76c2-1301">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1301">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="c76c2-1302">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1302">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="c76c2-1303">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="c76c2-1303">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="c76c2-1304">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1304">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="c76c2-1305">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1305">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="c76c2-1306">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="c76c2-1306">Virtual Directories</span></span>

<span data-ttu-id="c76c2-1307">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1307">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="c76c2-1308">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1308">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="c76c2-1309">子应用程序</span><span class="sxs-lookup"><span data-stu-id="c76c2-1309">Sub-applications</span></span>

<span data-ttu-id="c76c2-1310">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1310">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="c76c2-1311">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1311">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="c76c2-1312">子应用不应将 ASP.NET Core 模块作为处理程序包含在其中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1312">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="c76c2-1313">如果在子应用的 web.config  文件中将该模块添加为处理程序，则在尝试浏览子应用时会收到“500.19 内部服务器错误”  ，即引用错误的配置文件。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1313">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="c76c2-1314">以下示例显示 ASP.NET Core 子应用的已发布 web.config  文件：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1314">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="c76c2-1315">在 ASP.NET Core 应用下托管非 ASP.NET Core 子应用时，需在子应用的 web.config 文件中显式删除继承的处理程序  ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1315">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="c76c2-1316">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1316">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="c76c2-1317">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1317">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="c76c2-1318">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1318">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="c76c2-1319">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1319">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="c76c2-1320">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1320">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="c76c2-1321">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1321">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="c76c2-1322">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”  响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1322">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="c76c2-1323">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1323">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="c76c2-1324">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1324">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="c76c2-1325">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1325">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="c76c2-1326">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1326">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="c76c2-1327">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-1327">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="c76c2-1328">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。   </span><span class="sxs-lookup"><span data-stu-id="c76c2-1328">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="c76c2-1329">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1329">Select **OK**.</span></span>

<span data-ttu-id="c76c2-1330">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1330">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="c76c2-1331">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1331">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="c76c2-1332">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="c76c2-1332">Configuration of IIS with web.config</span></span>

<span data-ttu-id="c76c2-1333">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config  的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1333">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="c76c2-1334">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1334">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="c76c2-1335">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config  文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1335">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="c76c2-1336">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1336">For more information, see the following topics:</span></span>

* [<span data-ttu-id="c76c2-1337">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="c76c2-1337">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="c76c2-1338">若要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”  部分。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1338">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="c76c2-1339">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="c76c2-1339">Configuration sections of web.config</span></span>

<span data-ttu-id="c76c2-1340">ASP.NET Core 应用不会使用 web.config  中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1340">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="c76c2-1341">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1341">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="c76c2-1342">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1342">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="c76c2-1343">应用程序池</span><span class="sxs-lookup"><span data-stu-id="c76c2-1343">Application Pools</span></span>

<span data-ttu-id="c76c2-1344">在服务器上托管多个网站时，建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1344">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="c76c2-1345">IIS“添加网站”对话框默认执行此配置  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1345">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="c76c2-1346">提供了站点名称时，该文本会自动传输到“应用程序池”文本框   。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1346">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="c76c2-1347">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1347">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="c76c2-1348">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="c76c2-1348">Application Pool Identity</span></span>

<span data-ttu-id="c76c2-1349">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1349">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="c76c2-1350">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1350">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="c76c2-1351">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity    ：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1351">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="c76c2-1353">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1353">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="c76c2-1354">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1354">Resources can be secured using this identity.</span></span> <span data-ttu-id="c76c2-1355">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1355">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="c76c2-1356">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1356">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="c76c2-1357">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1357">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="c76c2-1358">右键单击该目录，然后选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1358">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="c76c2-1359">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮    。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1359">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="c76c2-1360">选择“位置”按钮，并确保该系统处于选中状态  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1360">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="c76c2-1361">在“输入要选择的对象名称”  区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1361">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="c76c2-1362">选择“检查名称”按钮  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1362">Select the **Check Names** button.</span></span> <span data-ttu-id="c76c2-1363">有关 DefaultAppPool  ，请检查使用 IIS AppPool\DefaultAppPool  的名称。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1363">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="c76c2-1364">当选择“检查名称”  按钮时，对象名称区域中会显示 DefaultAppPool  的值。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1364">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="c76c2-1365">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1365">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="c76c2-1366">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1366">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="c76c2-1368">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1368">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="c76c2-1370">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1370">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="c76c2-1371">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1371">Provide additional permissions as needed.</span></span>

<span data-ttu-id="c76c2-1372">也可使用 ICACLS  工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1372">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="c76c2-1373">以 DefaultAppPool  为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1373">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="c76c2-1374">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1374">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c76c2-1375">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="c76c2-1375">HTTP/2 support</span></span>

<span data-ttu-id="c76c2-1376">满足以下基本要求的进程外部署支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="c76c2-1376">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="c76c2-1377">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="c76c2-1377">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="c76c2-1378">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1378">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="c76c2-1379">目标框架：不适用于进程外部署，因为 HTTP/2 连接完全由 IIS 处理。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1379">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="c76c2-1380">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="c76c2-1380">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c76c2-1381">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1381">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="c76c2-1382">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1382">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c76c2-1383">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1383">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="c76c2-1384">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1384">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="c76c2-1385">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="c76c2-1385">CORS preflight requests</span></span>

<span data-ttu-id="c76c2-1386">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。 </span><span class="sxs-lookup"><span data-stu-id="c76c2-1386">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="c76c2-1387">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1387">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="c76c2-1388">若要了解如何在 web.config  中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="c76c2-1388">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="c76c2-1389">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="c76c2-1389">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="c76c2-1390">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="c76c2-1390">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="c76c2-1391">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="c76c2-1391">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="c76c2-1392">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="c76c2-1392">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="c76c2-1393">其他资源</span><span class="sxs-lookup"><span data-stu-id="c76c2-1393">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="c76c2-1394">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="c76c2-1394">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="c76c2-1395">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="c76c2-1395">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="c76c2-1396">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="c76c2-1396">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
