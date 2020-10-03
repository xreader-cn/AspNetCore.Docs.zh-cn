---
title: 使用 IIS 在 Windows 上托管 ASP.NET Core
author: rick-anderson
description: 了解如何在 Windows Server Internet Information Services (IIS) 上托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: f648837ce42bef4a828d7eda1a6abdfdd8ac07a2
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654031"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="2a708-103">使用 IIS 在 Windows 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a708-103">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2a708-104">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="2a708-104">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="2a708-105">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="2a708-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="2a708-106">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="2a708-106">Supported operating systems</span></span>

<span data-ttu-id="2a708-107">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="2a708-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="2a708-108">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2a708-108">Windows 7 or later</span></span>
* <span data-ttu-id="2a708-109">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2a708-109">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="2a708-110">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="2a708-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="2a708-111">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="2a708-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="2a708-112">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="2a708-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="2a708-113">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="2a708-113">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="2a708-114">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="2a708-114">Supported platforms</span></span>

<span data-ttu-id="2a708-115">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-115">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="2a708-116">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="2a708-116">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="2a708-117">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="2a708-117">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="2a708-118">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="2a708-118">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="2a708-119">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="2a708-119">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="2a708-120">为 32 位 (x86) 发布的应用必须已为其 IIS 应用程序池启用 32 位。</span><span class="sxs-lookup"><span data-stu-id="2a708-120">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="2a708-121">有关详细信息，请参阅[创建 IIS 站点](#create-the-iis-site)部分。</span><span class="sxs-lookup"><span data-stu-id="2a708-121">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="2a708-122">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-122">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="2a708-123">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-123">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2a708-124">托管模型</span><span class="sxs-lookup"><span data-stu-id="2a708-124">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="2a708-125">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="2a708-125">In-process hosting model</span></span>

<span data-ttu-id="2a708-126">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-126">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="2a708-127">进程内承载相较进程外承载提供更优的性能，因为请求并不通过环回适配器进行代理，环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="2a708-127">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="2a708-128">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="2a708-128">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2a708-129">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="2a708-129">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="2a708-130">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="2a708-130">Performs app initialization.</span></span>
  * <span data-ttu-id="2a708-131">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="2a708-131">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="2a708-132">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="2a708-132">Calls `Program.Main`.</span></span>
* <span data-ttu-id="2a708-133">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="2a708-133">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="2a708-134">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="2a708-134">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="2a708-136">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-136">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="2a708-137">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="2a708-137">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="2a708-138">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="2a708-138">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="2a708-139">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="2a708-139">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="2a708-140">在 IIS HTTP 服务器处理请求后：</span><span class="sxs-lookup"><span data-stu-id="2a708-140">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="2a708-141">请求被发送到 ASP.NET Core 中间件管道。</span><span class="sxs-lookup"><span data-stu-id="2a708-141">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="2a708-142">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="2a708-142">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="2a708-143">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="2a708-143">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="2a708-144">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="2a708-144">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="2a708-145">对于现有应用，进程内托管是可选功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-145">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="2a708-146">ASP.NET Core Web 模板使用进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="2a708-146">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="2a708-147">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。 </span><span class="sxs-lookup"><span data-stu-id="2a708-147">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="2a708-148">性能测试表明，与在进程外托管应用并将请求代理传入 [Kestrel](xref:fundamentals/servers/kestrel) 相比，在进程中托管 .NET Core 应用可提供明显更高的请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="2a708-148">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="2a708-149">作为单个文件可执行文件发布的应用无法由进程内托管模型加载。</span><span class="sxs-lookup"><span data-stu-id="2a708-149">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="2a708-150">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="2a708-150">Out-of-process hosting model</span></span>

<span data-ttu-id="2a708-151">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="2a708-151">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="2a708-152">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-152">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="2a708-153">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="2a708-153">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2a708-154">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="2a708-154">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="2a708-156">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-156">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="2a708-157">驱动程序将请求路由到网站的配置端口上的 IIS。</span><span class="sxs-lookup"><span data-stu-id="2a708-157">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="2a708-158">配置的端口通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="2a708-158">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="2a708-159">此模块将该请求转发到应用的随机端口上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2a708-159">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="2a708-160">随机端口不是 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="2a708-160">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="2a708-161">ASP.NET Core 模块在启动时通过环境变量指定端口。</span><span class="sxs-lookup"><span data-stu-id="2a708-161">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="2a708-162"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="2a708-162">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="2a708-163">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-163">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="2a708-164">此模块不支持 HTTPS 转发。</span><span class="sxs-lookup"><span data-stu-id="2a708-164">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="2a708-165">即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="2a708-165">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="2a708-166">Kestrel 从模块获取请求后，请求会被转发到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="2a708-166">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="2a708-167">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="2a708-167">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="2a708-168">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2a708-168">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="2a708-169">应用的响应传递回 IIS，IIS 将响应转发回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="2a708-169">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="2a708-170">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="2a708-170">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="2a708-171">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="2a708-171">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="2a708-172">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="2a708-172">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="2a708-173">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="2a708-173">Enable the IISIntegration components</span></span>

<span data-ttu-id="2a708-174">在 `CreateHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="2a708-174">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="2a708-175">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/generic-host#default-builder-settings>。</span><span class="sxs-lookup"><span data-stu-id="2a708-175">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="2a708-176">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="2a708-176">IIS options</span></span>

<span data-ttu-id="2a708-177">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="2a708-177">**In-process hosting model**</span></span>

<span data-ttu-id="2a708-178">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-178">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="2a708-179">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="2a708-179">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="2a708-180">选项</span><span class="sxs-lookup"><span data-stu-id="2a708-180">Option</span></span>                         | <span data-ttu-id="2a708-181">默认</span><span class="sxs-lookup"><span data-stu-id="2a708-181">Default</span></span> | <span data-ttu-id="2a708-182">设置</span><span class="sxs-lookup"><span data-stu-id="2a708-182">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="2a708-183">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="2a708-183">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="2a708-184">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="2a708-184">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="2a708-185">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-185">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="2a708-186">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-186">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="2a708-187">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="2a708-187">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="2a708-188">`HttpContext.Request` 和 `HttpContext.Response` 是否允许同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="2a708-188">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="2a708-189">获取或设置 `HttpRequest` 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="2a708-189">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="2a708-190">请注意，IIS 本身有限制 `maxAllowedContentLength`，这一限制将在 `IISServerOptions` 中设置 `MaxRequestBodySize` 之前进行处理。</span><span class="sxs-lookup"><span data-stu-id="2a708-190">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="2a708-191">更改 `MaxRequestBodySize` 不会影响 `maxAllowedContentLength`。</span><span class="sxs-lookup"><span data-stu-id="2a708-191">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="2a708-192">若要增加 `maxAllowedContentLength`，请在 web.config 中添加一个条目，将 `maxAllowedContentLength` 设置为更高值。</span><span class="sxs-lookup"><span data-stu-id="2a708-192">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="2a708-193">有关更多详细信息，请参阅[配置](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。</span><span class="sxs-lookup"><span data-stu-id="2a708-193">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="2a708-194">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="2a708-194">**Out-of-process hosting model**</span></span>

<span data-ttu-id="2a708-195">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-195">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="2a708-196">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="2a708-196">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="2a708-197">选项</span><span class="sxs-lookup"><span data-stu-id="2a708-197">Option</span></span>                         | <span data-ttu-id="2a708-198">默认</span><span class="sxs-lookup"><span data-stu-id="2a708-198">Default</span></span> | <span data-ttu-id="2a708-199">设置</span><span class="sxs-lookup"><span data-stu-id="2a708-199">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="2a708-200">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="2a708-200">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="2a708-201">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="2a708-201">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="2a708-202">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-202">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="2a708-203">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="2a708-203">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="2a708-204">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="2a708-204">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="2a708-205">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="2a708-205">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2a708-206">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="2a708-206">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2a708-207">将 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块配置为转发：</span><span class="sxs-lookup"><span data-stu-id="2a708-207">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="2a708-208">方案 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="2a708-208">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="2a708-209">发起请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="2a708-209">Remote IP address where the request originated.</span></span>

<span data-ttu-id="2a708-210">[IIS 集成中间件](#enable-the-iisintegration-components)配置转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="2a708-210">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="2a708-211">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-211">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="2a708-212">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="2a708-212">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="2a708-213">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="2a708-213">web.config file</span></span>

<span data-ttu-id="2a708-214">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="2a708-214">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="2a708-215">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-215">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="2a708-216">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="2a708-216">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="2a708-217">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="2a708-217">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="2a708-218">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)  。</span><span class="sxs-lookup"><span data-stu-id="2a708-218">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="2a708-219">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="2a708-219">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="2a708-220">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="2a708-220">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="2a708-221">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="2a708-221">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="2a708-222">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="2a708-222">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="2a708-223">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="2a708-223">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="2a708-224">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="2a708-224">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="2a708-225">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="2a708-225">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="2a708-226">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="2a708-226">web.config file location</span></span>

<span data-ttu-id="2a708-227">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config 文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="2a708-227">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="2a708-228">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="2a708-228">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="2a708-229">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-229">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="2a708-230">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="2a708-230">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="2a708-231">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="2a708-231">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="2a708-232">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-232">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="2a708-233">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="2a708-233">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="2a708-234">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="2a708-234">Transform web.config</span></span>

<span data-ttu-id="2a708-235">如果需要在发布时转换 web.config，请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="2a708-235">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="2a708-236">你可能需要在发布时转换 web.config，以便基于配置、配置文件或环境设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="2a708-236">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="2a708-237">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="2a708-237">IIS configuration</span></span>

<span data-ttu-id="2a708-238">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="2a708-238">**Windows Server operating systems**</span></span>

<span data-ttu-id="2a708-239">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="2a708-239">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="2a708-240">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="2a708-240">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="2a708-241">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="2a708-241">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="2a708-243">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="2a708-243">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="2a708-244">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="2a708-244">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="2a708-246">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-246">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="2a708-247">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="2a708-247">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="2a708-248">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-248">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="2a708-249">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-249">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="2a708-250">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-250">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="2a708-251">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-251">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="2a708-252">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="2a708-252">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="2a708-253">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-253">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="2a708-254">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="2a708-254">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="2a708-255">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="2a708-255">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="2a708-256">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="2a708-256">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="2a708-257">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="2a708-257">**Windows desktop operating systems**</span></span>

<span data-ttu-id="2a708-258">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="2a708-258">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="2a708-259">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="2a708-259">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="2a708-260">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-260">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="2a708-261">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-261">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="2a708-262">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="2a708-262">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="2a708-263">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="2a708-263">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="2a708-264">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-264">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="2a708-265">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-265">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="2a708-266">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="2a708-266">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="2a708-267">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-267">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="2a708-268">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-268">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="2a708-269">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-269">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="2a708-270">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-270">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="2a708-271">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="2a708-271">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="2a708-272">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-272">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="2a708-273">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="2a708-273">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="2a708-274">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="2a708-274">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="2a708-276">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="2a708-276">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="2a708-277">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="2a708-277">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="2a708-278">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="2a708-278">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="2a708-279">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-279">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2a708-280">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="2a708-280">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="2a708-281">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-281">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="2a708-282">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="2a708-282">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="2a708-283">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="2a708-283">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="2a708-284">直接下载（当前版本）</span><span class="sxs-lookup"><span data-stu-id="2a708-284">Direct download (current version)</span></span>

<span data-ttu-id="2a708-285">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="2a708-285">Download the installer using the following link:</span></span>

[<span data-ttu-id="2a708-286">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="2a708-286">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="2a708-287">先前版本的安装程序</span><span class="sxs-lookup"><span data-stu-id="2a708-287">Earlier versions of the installer</span></span>

<span data-ttu-id="2a708-288">若要获取先前版本的安装程序：</span><span class="sxs-lookup"><span data-stu-id="2a708-288">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="2a708-289">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="2a708-289">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="2a708-290">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-290">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="2a708-291">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="2a708-291">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="2a708-292">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-292">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="2a708-293">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-293">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="2a708-294">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="2a708-294">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="2a708-295">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="2a708-295">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="2a708-296">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-296">Run the installer on the server.</span></span> <span data-ttu-id="2a708-297">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="2a708-297">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="2a708-298">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="2a708-298">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="2a708-299">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-299">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="2a708-300">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="2a708-300">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="2a708-301">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="2a708-301">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="2a708-302">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="2a708-302">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="2a708-303">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-303">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="2a708-304">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="2a708-304">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="2a708-305">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-305">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="2a708-306">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (applicationHost.config) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="2a708-306">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="2a708-307">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-307">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="2a708-308">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="2a708-308">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="2a708-309">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2a708-309">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="2a708-310">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="2a708-310">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="2a708-311">ASP.NET Core 不采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="2a708-311">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="2a708-312">通过安装新的托管捆绑包升级共享框架后，请重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2a708-312">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="2a708-313">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="2a708-313">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="2a708-314">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="2a708-314">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="2a708-315">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="2a708-315">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="2a708-316">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-316">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="2a708-317">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="2a708-317">The preferred method is to use WebPI.</span></span> <span data-ttu-id="2a708-318">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-318">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="2a708-319">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="2a708-319">Create the IIS site</span></span>

1. <span data-ttu-id="2a708-320">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-320">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="2a708-321">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="2a708-321">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="2a708-322">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="2a708-322">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="2a708-323">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-323">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="2a708-324">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="2a708-324">Right-click the **Sites** folder.</span></span> <span data-ttu-id="2a708-325">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="2a708-325">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="2a708-326">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="2a708-326">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="2a708-327">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="2a708-327">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="2a708-329">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="2a708-329">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="2a708-330">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="2a708-330">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="2a708-331">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="2a708-331">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="2a708-332">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="2a708-332">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="2a708-333">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="2a708-333">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="2a708-334">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="2a708-334">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="2a708-335">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="2a708-335">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="2a708-336">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="2a708-336">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="2a708-337">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="2a708-337">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="2a708-339">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-339">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="2a708-340">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载。</span><span class="sxs-lookup"><span data-stu-id="2a708-340">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="2a708-341">将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR)，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-341">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="2a708-342">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="2a708-342">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="2a708-343">*ASP.NET Core 2.2 或更高版本*：</span><span class="sxs-lookup"><span data-stu-id="2a708-343">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="2a708-344">对于使用 32 位 SDK 发布的 32 位 (x86) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，且该 SDK 使用[进程内托管模型](#in-process-hosting-model)，请为 32 位启用应用程序池。</span><span class="sxs-lookup"><span data-stu-id="2a708-344">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="2a708-345">在 IIS 管理器中，导航到“连接”边栏中的“应用程序池” 。</span><span class="sxs-lookup"><span data-stu-id="2a708-345">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="2a708-346">选择应用的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="2a708-346">Select the app's Application Pool.</span></span> <span data-ttu-id="2a708-347">在“操作”边栏中，选择，“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="2a708-347">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="2a708-348">将“启用 32 位应用程序”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="2a708-348">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="2a708-349">对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-349">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="2a708-350">在 IIS 管理器中，导航到“连接”边栏中的“应用程序池” 。</span><span class="sxs-lookup"><span data-stu-id="2a708-350">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="2a708-351">选择应用的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="2a708-351">Select the app's Application Pool.</span></span> <span data-ttu-id="2a708-352">在“操作”边栏中，选择，“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="2a708-352">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="2a708-353">将“启用 32 位应用程序”设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="2a708-353">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="2a708-354">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-354">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="2a708-355">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="2a708-355">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="2a708-356">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-356">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="2a708-357">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-357">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="2a708-358">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-358">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="2a708-359">部署应用</span><span class="sxs-lookup"><span data-stu-id="2a708-359">Deploy the app</span></span>

<span data-ttu-id="2a708-360">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="2a708-360">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="2a708-361">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="2a708-361">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="2a708-362">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="2a708-362">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="2a708-363">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="2a708-363">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="2a708-364">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="2a708-364">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="2a708-366">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="2a708-366">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="2a708-367">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="2a708-367">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="2a708-368">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="2a708-368">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="2a708-369">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="2a708-369">Alternatives to Web Deploy</span></span>

<span data-ttu-id="2a708-370">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="2a708-370">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="2a708-371">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="2a708-371">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="2a708-372">浏览网站</span><span class="sxs-lookup"><span data-stu-id="2a708-372">Browse the website</span></span>

<span data-ttu-id="2a708-373">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-373">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="2a708-374">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="2a708-374">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="2a708-375">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="2a708-375">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="2a708-377">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="2a708-377">Locked deployment files</span></span>

<span data-ttu-id="2a708-378">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="2a708-378">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="2a708-379">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-379">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="2a708-380">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="2a708-380">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="2a708-381">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="2a708-381">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="2a708-382">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-382">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="2a708-383">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-383">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="2a708-384">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="2a708-384">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="2a708-385">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-385">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="2a708-386">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="2a708-386">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="2a708-387">数据保护</span><span class="sxs-lookup"><span data-stu-id="2a708-387">Data protection</span></span>

<span data-ttu-id="2a708-388">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="2a708-388">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="2a708-389">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="2a708-389">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="2a708-390">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="2a708-390">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="2a708-391">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="2a708-391">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="2a708-392">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="2a708-392">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="2a708-393">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="2a708-393">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="2a708-394">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="2a708-394">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="2a708-395">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="2a708-395">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="2a708-396">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="2a708-396">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="2a708-397">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="2a708-397">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="2a708-398">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="2a708-398">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="2a708-399">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="2a708-399">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="2a708-400">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="2a708-400">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="2a708-401">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="2a708-401">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="2a708-402">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="2a708-402">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="2a708-403">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="2a708-403">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="2a708-404">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="2a708-404">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="2a708-405">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="2a708-405">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="2a708-406">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="2a708-406">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="2a708-407">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="2a708-407">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="2a708-408">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="2a708-408">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="2a708-409">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="2a708-409">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="2a708-410">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="2a708-410">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="2a708-411">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="2a708-411">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="2a708-412">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="2a708-412">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="2a708-413">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2a708-413">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="2a708-414">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="2a708-414">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="2a708-415">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2a708-415">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="2a708-416">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2a708-416">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="2a708-417">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2a708-417">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="2a708-418">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2a708-418">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="2a708-419">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-419">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="2a708-420">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="2a708-420">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="2a708-421">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2a708-421">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="2a708-422">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="2a708-422">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="2a708-423">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="2a708-423">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="2a708-424">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="2a708-424">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="2a708-425">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="2a708-425">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="2a708-426">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="2a708-426">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="2a708-427">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="2a708-427">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="2a708-428">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="2a708-428">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="2a708-429">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="2a708-429">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="2a708-430">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="2a708-430">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="2a708-431">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="2a708-431">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="2a708-432">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="2a708-432">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="2a708-433">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="2a708-433">Virtual Directories</span></span>

<span data-ttu-id="2a708-434">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="2a708-434">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="2a708-435">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="2a708-435">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="2a708-436">子应用程序</span><span class="sxs-lookup"><span data-stu-id="2a708-436">Sub-applications</span></span>

<span data-ttu-id="2a708-437">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="2a708-437">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="2a708-438">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="2a708-438">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="2a708-439">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="2a708-439">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="2a708-440">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="2a708-440">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="2a708-441">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="2a708-441">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="2a708-442">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-442">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="2a708-443">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="2a708-443">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="2a708-444">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="2a708-444">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="2a708-445">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="2a708-445">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="2a708-446">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="2a708-446">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="2a708-447">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-447">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="2a708-448">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="2a708-448">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="2a708-449">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2a708-449">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="2a708-450">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="2a708-450">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="2a708-451">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="2a708-451">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="2a708-452">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-452">Select **OK**.</span></span>

<span data-ttu-id="2a708-453">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-453">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="2a708-454">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="2a708-454">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="2a708-455">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="2a708-455">Configuration of IIS with web.config</span></span>

<span data-ttu-id="2a708-456">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="2a708-456">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="2a708-457">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="2a708-457">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="2a708-458">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="2a708-458">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="2a708-459">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="2a708-459">For more information, see the following topics:</span></span>

* [<span data-ttu-id="2a708-460">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="2a708-460">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="2a708-461">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="2a708-461">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="2a708-462">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="2a708-462">Configuration sections of web.config</span></span>

<span data-ttu-id="2a708-463">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="2a708-463">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="2a708-464">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-464">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="2a708-465">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="2a708-465">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="2a708-466">应用程序池</span><span class="sxs-lookup"><span data-stu-id="2a708-466">Application Pools</span></span>

<span data-ttu-id="2a708-467">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="2a708-467">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="2a708-468">托管在进程内：应用需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-468">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="2a708-469">托管在进程外：建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="2a708-469">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="2a708-470">IIS“添加网站”对话框默认为每应用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-470">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="2a708-471">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="2a708-471">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="2a708-472">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-472">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="2a708-473">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="2a708-473">Application Pool Identity</span></span>

<span data-ttu-id="2a708-474">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="2a708-474">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="2a708-475">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="2a708-475">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="2a708-476">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="2a708-476">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="2a708-478">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="2a708-478">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="2a708-479">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="2a708-479">Resources can be secured using this identity.</span></span> <span data-ttu-id="2a708-480">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="2a708-480">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="2a708-481">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="2a708-481">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="2a708-482">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="2a708-482">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="2a708-483">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="2a708-483">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="2a708-484">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="2a708-484">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="2a708-485">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="2a708-485">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="2a708-486">在“输入要选择的对象名称”区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="2a708-486">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="2a708-487">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="2a708-487">Select the **Check Names** button.</span></span> <span data-ttu-id="2a708-488">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="2a708-488">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="2a708-489">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="2a708-489">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="2a708-490">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="2a708-490">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="2a708-491">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="2a708-491">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="2a708-493">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-493">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="2a708-495">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-495">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="2a708-496">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-496">Provide additional permissions as needed.</span></span>

<span data-ttu-id="2a708-497">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-497">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="2a708-498">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="2a708-498">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="2a708-499">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="2a708-499">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="2a708-500">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="2a708-500">CORS preflight requests</span></span>

<span data-ttu-id="2a708-501">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-501">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="2a708-502">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-502">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="2a708-503">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="2a708-503">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="2a708-504">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="2a708-504">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="2a708-505">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="2a708-505">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="2a708-506">[应用程序初始化模块](#application-initialization-module)：可以将托管在[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)的应用配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="2a708-506">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="2a708-507">[空闲超时](#idle-timeout)：可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段不超时。</span><span class="sxs-lookup"><span data-stu-id="2a708-507">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="2a708-508">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="2a708-508">Application Initialization Module</span></span>

<span data-ttu-id="2a708-509">适用于托管在进程内和进程外的应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-509">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="2a708-510">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-510">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="2a708-511">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="2a708-511">The request triggers the app to start.</span></span> <span data-ttu-id="2a708-512">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="2a708-512">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="2a708-513">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="2a708-513">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="2a708-514">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="2a708-514">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="2a708-515">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="2a708-515">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="2a708-516">打开“Internet Information Services”>“万维网服务”>“应用程序开发功能”。</span><span class="sxs-lookup"><span data-stu-id="2a708-516">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="2a708-517">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="2a708-517">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="2a708-518">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="2a708-518">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="2a708-519">打开“添加角色和功能向导”。</span><span class="sxs-lookup"><span data-stu-id="2a708-519">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="2a708-520">在“选择角色服务”面板中，打开“应用程序开发”节点 。</span><span class="sxs-lookup"><span data-stu-id="2a708-520">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="2a708-521">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="2a708-521">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="2a708-522">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="2a708-522">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="2a708-523">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="2a708-523">Using IIS Manager:</span></span>

  1. <span data-ttu-id="2a708-524">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="2a708-524">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="2a708-525">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="2a708-525">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="2a708-526">默认的“启动模式”为“按需” 。</span><span class="sxs-lookup"><span data-stu-id="2a708-526">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="2a708-527">将“启动模式”设置为“始终运行”。</span><span class="sxs-lookup"><span data-stu-id="2a708-527">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="2a708-528">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-528">Select **OK**.</span></span>
  1. <span data-ttu-id="2a708-529">打开“连接”面板中的“网站”节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-529">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="2a708-530">右键单击应用，并选择“管理网站”>“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="2a708-530">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="2a708-531">默认的“预加载已启用”设置为“False” 。</span><span class="sxs-lookup"><span data-stu-id="2a708-531">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="2a708-532">将“预加载已启用”设置为“True”。</span><span class="sxs-lookup"><span data-stu-id="2a708-532">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="2a708-533">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-533">Select **OK**.</span></span>

* <span data-ttu-id="2a708-534">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中 ：</span><span class="sxs-lookup"><span data-stu-id="2a708-534">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="2a708-535">空闲超时</span><span class="sxs-lookup"><span data-stu-id="2a708-535">Idle Timeout</span></span>

<span data-ttu-id="2a708-536">仅适用于托管在进程内的应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-536">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="2a708-537">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="2a708-537">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="2a708-538">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="2a708-538">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="2a708-539">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="2a708-539">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="2a708-540">默认的“空闲超时(分钟)”为“20”分钟 。</span><span class="sxs-lookup"><span data-stu-id="2a708-540">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="2a708-541">将“空闲超时(分钟)”设置为“0”（零） 。</span><span class="sxs-lookup"><span data-stu-id="2a708-541">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="2a708-542">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-542">Select **OK**.</span></span>
1. <span data-ttu-id="2a708-543">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="2a708-543">Recycle the worker process.</span></span>

<span data-ttu-id="2a708-544">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="2a708-544">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="2a708-545">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="2a708-545">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="2a708-546">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="2a708-546">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="2a708-547">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="2a708-547">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="2a708-548">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="2a708-548">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="2a708-549">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="2a708-549">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="2a708-550">[应用程序池 \<processModel> 的进程模型设置](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="2a708-550">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="2a708-551">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="2a708-551">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="2a708-552">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="2a708-552">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="2a708-553">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="2a708-553">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="2a708-554">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="2a708-554">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="2a708-555">其他资源</span><span class="sxs-lookup"><span data-stu-id="2a708-555">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="2a708-556">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="2a708-556">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="2a708-557">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="2a708-557">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="2a708-558">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="2a708-558">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2a708-559">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="2a708-559">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="2a708-560">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="2a708-560">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="2a708-561">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="2a708-561">Supported operating systems</span></span>

<span data-ttu-id="2a708-562">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="2a708-562">The following operating systems are supported:</span></span>

* <span data-ttu-id="2a708-563">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2a708-563">Windows 7 or later</span></span>
* <span data-ttu-id="2a708-564">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2a708-564">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2a708-565">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="2a708-565">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="2a708-566">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="2a708-566">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="2a708-567">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="2a708-567">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="2a708-568">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="2a708-568">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="2a708-569">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="2a708-569">Supported platforms</span></span>

<span data-ttu-id="2a708-570">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-570">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="2a708-571">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="2a708-571">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="2a708-572">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="2a708-572">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="2a708-573">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="2a708-573">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="2a708-574">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="2a708-574">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="2a708-575">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-575">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="2a708-576">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-576">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2a708-577">托管模型</span><span class="sxs-lookup"><span data-stu-id="2a708-577">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="2a708-578">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="2a708-578">In-process hosting model</span></span>

<span data-ttu-id="2a708-579">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-579">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="2a708-580">进程内托管的性能高于进程外托管的性能，因为：</span><span class="sxs-lookup"><span data-stu-id="2a708-580">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="2a708-581">请求并不通过环回适配器进行代理。</span><span class="sxs-lookup"><span data-stu-id="2a708-581">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="2a708-582">环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="2a708-582">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="2a708-583">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="2a708-583">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2a708-584">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="2a708-584">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="2a708-585">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="2a708-585">Performs app initialization.</span></span>
  * <span data-ttu-id="2a708-586">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="2a708-586">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="2a708-587">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="2a708-587">Calls `Program.Main`.</span></span>
* <span data-ttu-id="2a708-588">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="2a708-588">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="2a708-589">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="2a708-589">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="2a708-590">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="2a708-590">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

<span data-ttu-id="2a708-592">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-592">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="2a708-593">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="2a708-593">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="2a708-594">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="2a708-594">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="2a708-595">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="2a708-595">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="2a708-596">IIS HTTP 服务器处理请求之后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="2a708-596">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="2a708-597">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="2a708-597">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="2a708-598">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="2a708-598">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="2a708-599">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="2a708-599">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="2a708-600">进程内托管选择使用现有应用，但 [dotnet new](/dotnet/core/tools/dotnet-new) 模板默认使用所有 IIS 和 IIS Express 方案的进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="2a708-600">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="2a708-601">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。 </span><span class="sxs-lookup"><span data-stu-id="2a708-601">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="2a708-602">性能测试表明，与在进程外托管应用并将请求代理到 [Kestrel](xref:fundamentals/servers/kestrel) 服务器相比，在进程中托管 .NET Core 应用可以大大提升请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="2a708-602">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="2a708-603">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="2a708-603">Out-of-process hosting model</span></span>

<span data-ttu-id="2a708-604">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="2a708-604">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="2a708-605">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-605">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="2a708-606">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="2a708-606">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2a708-607">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="2a708-607">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="2a708-609">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-609">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="2a708-610">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="2a708-610">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="2a708-611">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2a708-611">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="2a708-612">该模块在启动时通过环境变量指定端口，<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="2a708-612">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="2a708-613">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-613">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="2a708-614">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="2a708-614">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="2a708-615">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="2a708-615">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="2a708-616">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="2a708-616">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="2a708-617">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2a708-617">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="2a708-618">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="2a708-618">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="2a708-619">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="2a708-619">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="2a708-620">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="2a708-620">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="2a708-621">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="2a708-621">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="2a708-622">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="2a708-622">Enable the IISIntegration components</span></span>

<span data-ttu-id="2a708-623">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="2a708-623">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="2a708-624">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="2a708-624">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="2a708-625">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="2a708-625">IIS options</span></span>

<span data-ttu-id="2a708-626">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="2a708-626">**In-process hosting model**</span></span>

<span data-ttu-id="2a708-627">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-627">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="2a708-628">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="2a708-628">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="2a708-629">选项</span><span class="sxs-lookup"><span data-stu-id="2a708-629">Option</span></span>                         | <span data-ttu-id="2a708-630">默认</span><span class="sxs-lookup"><span data-stu-id="2a708-630">Default</span></span> | <span data-ttu-id="2a708-631">设置</span><span class="sxs-lookup"><span data-stu-id="2a708-631">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="2a708-632">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="2a708-632">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="2a708-633">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="2a708-633">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="2a708-634">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-634">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="2a708-635">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-635">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="2a708-636">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="2a708-636">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="2a708-637">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="2a708-637">**Out-of-process hosting model**</span></span>

<span data-ttu-id="2a708-638">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-638">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="2a708-639">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="2a708-639">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="2a708-640">选项</span><span class="sxs-lookup"><span data-stu-id="2a708-640">Option</span></span>                         | <span data-ttu-id="2a708-641">默认</span><span class="sxs-lookup"><span data-stu-id="2a708-641">Default</span></span> | <span data-ttu-id="2a708-642">设置</span><span class="sxs-lookup"><span data-stu-id="2a708-642">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="2a708-643">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="2a708-643">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="2a708-644">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="2a708-644">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="2a708-645">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-645">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="2a708-646">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="2a708-646">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="2a708-647">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="2a708-647">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="2a708-648">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="2a708-648">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2a708-649">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="2a708-649">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2a708-650">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="2a708-650">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="2a708-651">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-651">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="2a708-652">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="2a708-652">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="2a708-653">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="2a708-653">web.config file</span></span>

<span data-ttu-id="2a708-654">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="2a708-654">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="2a708-655">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-655">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="2a708-656">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="2a708-656">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="2a708-657">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="2a708-657">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="2a708-658">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)  。</span><span class="sxs-lookup"><span data-stu-id="2a708-658">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="2a708-659">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="2a708-659">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="2a708-660">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="2a708-660">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="2a708-661">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="2a708-661">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="2a708-662">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="2a708-662">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="2a708-663">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="2a708-663">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="2a708-664">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="2a708-664">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="2a708-665">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="2a708-665">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="2a708-666">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="2a708-666">web.config file location</span></span>

<span data-ttu-id="2a708-667">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config 文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="2a708-667">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="2a708-668">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="2a708-668">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="2a708-669">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-669">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="2a708-670">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="2a708-670">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="2a708-671">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="2a708-671">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="2a708-672">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-672">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="2a708-673">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="2a708-673">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="2a708-674">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="2a708-674">Transform web.config</span></span>

<span data-ttu-id="2a708-675">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="2a708-675">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="2a708-676">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="2a708-676">IIS configuration</span></span>

<span data-ttu-id="2a708-677">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="2a708-677">**Windows Server operating systems**</span></span>

<span data-ttu-id="2a708-678">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="2a708-678">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="2a708-679">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="2a708-679">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="2a708-680">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="2a708-680">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="2a708-682">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="2a708-682">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="2a708-683">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="2a708-683">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="2a708-685">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-685">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="2a708-686">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="2a708-686">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="2a708-687">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-687">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="2a708-688">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-688">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="2a708-689">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-689">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="2a708-690">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-690">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="2a708-691">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="2a708-691">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="2a708-692">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-692">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="2a708-693">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="2a708-693">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="2a708-694">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="2a708-694">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="2a708-695">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="2a708-695">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="2a708-696">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="2a708-696">**Windows desktop operating systems**</span></span>

<span data-ttu-id="2a708-697">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="2a708-697">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="2a708-698">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="2a708-698">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="2a708-699">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-699">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="2a708-700">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-700">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="2a708-701">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="2a708-701">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="2a708-702">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="2a708-702">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="2a708-703">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-703">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="2a708-704">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-704">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="2a708-705">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="2a708-705">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="2a708-706">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-706">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="2a708-707">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-707">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="2a708-708">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-708">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="2a708-709">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-709">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="2a708-710">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="2a708-710">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="2a708-711">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-711">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="2a708-712">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="2a708-712">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="2a708-713">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="2a708-713">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="2a708-715">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="2a708-715">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="2a708-716">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="2a708-716">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="2a708-717">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="2a708-717">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="2a708-718">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-718">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2a708-719">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="2a708-719">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="2a708-720">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-720">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="2a708-721">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="2a708-721">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="2a708-722">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="2a708-722">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="2a708-723">下载</span><span class="sxs-lookup"><span data-stu-id="2a708-723">Download</span></span>

1. <span data-ttu-id="2a708-724">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="2a708-724">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="2a708-725">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-725">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="2a708-726">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="2a708-726">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="2a708-727">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-727">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="2a708-728">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-728">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="2a708-729">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="2a708-729">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="2a708-730">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="2a708-730">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="2a708-731">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-731">Run the installer on the server.</span></span> <span data-ttu-id="2a708-732">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="2a708-732">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="2a708-733">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="2a708-733">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="2a708-734">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-734">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="2a708-735">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="2a708-735">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="2a708-736">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="2a708-736">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="2a708-737">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="2a708-737">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="2a708-738">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-738">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="2a708-739">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="2a708-739">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="2a708-740">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-740">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="2a708-741">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (applicationHost.config) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="2a708-741">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="2a708-742">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-742">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="2a708-743">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="2a708-743">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="2a708-744">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2a708-744">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="2a708-745">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="2a708-745">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="2a708-746">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="2a708-746">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="2a708-747">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="2a708-747">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="2a708-748">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="2a708-748">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="2a708-749">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="2a708-749">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="2a708-750">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="2a708-750">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="2a708-751">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="2a708-751">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="2a708-752">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="2a708-752">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="2a708-753">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="2a708-753">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="2a708-754">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="2a708-754">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="2a708-755">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-755">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="2a708-756">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="2a708-756">The preferred method is to use WebPI.</span></span> <span data-ttu-id="2a708-757">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-757">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="2a708-758">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="2a708-758">Create the IIS site</span></span>

1. <span data-ttu-id="2a708-759">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-759">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="2a708-760">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="2a708-760">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="2a708-761">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="2a708-761">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="2a708-762">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-762">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="2a708-763">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="2a708-763">Right-click the **Sites** folder.</span></span> <span data-ttu-id="2a708-764">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="2a708-764">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="2a708-765">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="2a708-765">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="2a708-766">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="2a708-766">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="2a708-768">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="2a708-768">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="2a708-769">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="2a708-769">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="2a708-770">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="2a708-770">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="2a708-771">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="2a708-771">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="2a708-772">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="2a708-772">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="2a708-773">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="2a708-773">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="2a708-774">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="2a708-774">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="2a708-775">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="2a708-775">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="2a708-776">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="2a708-776">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="2a708-778">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-778">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="2a708-779">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-779">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="2a708-780">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="2a708-780">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="2a708-781">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-781">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="2a708-782">在 IIS 管理器 >“应用程序池”的“操作”侧栏中，选择“设置应用程序池默认设置”或“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="2a708-782">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="2a708-783">找到“启用 32 位应用程序”并将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="2a708-783">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="2a708-784">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-784">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="2a708-785">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-785">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="2a708-786">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="2a708-786">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="2a708-787">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-787">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="2a708-788">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-788">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="2a708-789">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-789">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="2a708-790">部署应用</span><span class="sxs-lookup"><span data-stu-id="2a708-790">Deploy the app</span></span>

<span data-ttu-id="2a708-791">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="2a708-791">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="2a708-792">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="2a708-792">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="2a708-793">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="2a708-793">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="2a708-794">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="2a708-794">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="2a708-795">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="2a708-795">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="2a708-797">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="2a708-797">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="2a708-798">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="2a708-798">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="2a708-799">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="2a708-799">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="2a708-800">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="2a708-800">Alternatives to Web Deploy</span></span>

<span data-ttu-id="2a708-801">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="2a708-801">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="2a708-802">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="2a708-802">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="2a708-803">浏览网站</span><span class="sxs-lookup"><span data-stu-id="2a708-803">Browse the website</span></span>

<span data-ttu-id="2a708-804">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-804">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="2a708-805">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="2a708-805">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="2a708-806">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="2a708-806">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="2a708-808">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="2a708-808">Locked deployment files</span></span>

<span data-ttu-id="2a708-809">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="2a708-809">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="2a708-810">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-810">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="2a708-811">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="2a708-811">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="2a708-812">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="2a708-812">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="2a708-813">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-813">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="2a708-814">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-814">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="2a708-815">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="2a708-815">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="2a708-816">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-816">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="2a708-817">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="2a708-817">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="2a708-818">数据保护</span><span class="sxs-lookup"><span data-stu-id="2a708-818">Data protection</span></span>

<span data-ttu-id="2a708-819">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="2a708-819">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="2a708-820">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="2a708-820">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="2a708-821">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="2a708-821">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="2a708-822">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="2a708-822">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="2a708-823">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="2a708-823">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="2a708-824">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="2a708-824">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="2a708-825">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="2a708-825">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="2a708-826">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="2a708-826">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="2a708-827">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="2a708-827">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="2a708-828">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="2a708-828">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="2a708-829">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="2a708-829">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="2a708-830">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="2a708-830">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="2a708-831">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="2a708-831">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="2a708-832">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="2a708-832">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="2a708-833">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="2a708-833">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="2a708-834">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="2a708-834">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="2a708-835">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="2a708-835">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="2a708-836">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="2a708-836">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="2a708-837">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="2a708-837">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="2a708-838">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="2a708-838">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="2a708-839">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="2a708-839">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="2a708-840">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="2a708-840">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="2a708-841">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="2a708-841">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="2a708-842">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="2a708-842">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="2a708-843">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="2a708-843">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="2a708-844">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2a708-844">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="2a708-845">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="2a708-845">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="2a708-846">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2a708-846">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="2a708-847">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2a708-847">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="2a708-848">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2a708-848">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="2a708-849">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2a708-849">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="2a708-850">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-850">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="2a708-851">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="2a708-851">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="2a708-852">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2a708-852">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="2a708-853">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="2a708-853">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="2a708-854">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="2a708-854">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="2a708-855">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="2a708-855">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="2a708-856">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="2a708-856">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="2a708-857">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="2a708-857">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="2a708-858">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="2a708-858">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="2a708-859">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="2a708-859">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="2a708-860">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="2a708-860">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="2a708-861">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="2a708-861">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="2a708-862">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="2a708-862">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="2a708-863">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="2a708-863">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="2a708-864">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="2a708-864">Virtual Directories</span></span>

<span data-ttu-id="2a708-865">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="2a708-865">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="2a708-866">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="2a708-866">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="2a708-867">子应用程序</span><span class="sxs-lookup"><span data-stu-id="2a708-867">Sub-applications</span></span>

<span data-ttu-id="2a708-868">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="2a708-868">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="2a708-869">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="2a708-869">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="2a708-870">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="2a708-870">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="2a708-871">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="2a708-871">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="2a708-872">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="2a708-872">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="2a708-873">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-873">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="2a708-874">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="2a708-874">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="2a708-875">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="2a708-875">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="2a708-876">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="2a708-876">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="2a708-877">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="2a708-877">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="2a708-878">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-878">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="2a708-879">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="2a708-879">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="2a708-880">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2a708-880">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="2a708-881">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="2a708-881">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="2a708-882">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="2a708-882">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="2a708-883">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-883">Select **OK**.</span></span>

<span data-ttu-id="2a708-884">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-884">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="2a708-885">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="2a708-885">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="2a708-886">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="2a708-886">Configuration of IIS with web.config</span></span>

<span data-ttu-id="2a708-887">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="2a708-887">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="2a708-888">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="2a708-888">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="2a708-889">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="2a708-889">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="2a708-890">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="2a708-890">For more information, see the following topics:</span></span>

* [<span data-ttu-id="2a708-891">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="2a708-891">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="2a708-892">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="2a708-892">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="2a708-893">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="2a708-893">Configuration sections of web.config</span></span>

<span data-ttu-id="2a708-894">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="2a708-894">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="2a708-895">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-895">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="2a708-896">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="2a708-896">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="2a708-897">应用程序池</span><span class="sxs-lookup"><span data-stu-id="2a708-897">Application Pools</span></span>

<span data-ttu-id="2a708-898">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="2a708-898">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="2a708-899">托管在进程内：应用需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-899">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="2a708-900">托管在进程外：建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="2a708-900">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="2a708-901">IIS“添加网站”对话框默认为每应用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-901">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="2a708-902">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="2a708-902">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="2a708-903">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-903">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="2a708-904">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="2a708-904">Application Pool Identity</span></span>

<span data-ttu-id="2a708-905">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="2a708-905">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="2a708-906">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="2a708-906">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="2a708-907">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="2a708-907">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="2a708-909">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="2a708-909">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="2a708-910">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="2a708-910">Resources can be secured using this identity.</span></span> <span data-ttu-id="2a708-911">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="2a708-911">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="2a708-912">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="2a708-912">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="2a708-913">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="2a708-913">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="2a708-914">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="2a708-914">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="2a708-915">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="2a708-915">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="2a708-916">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="2a708-916">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="2a708-917">在“输入要选择的对象名称”区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="2a708-917">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="2a708-918">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="2a708-918">Select the **Check Names** button.</span></span> <span data-ttu-id="2a708-919">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="2a708-919">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="2a708-920">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="2a708-920">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="2a708-921">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="2a708-921">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="2a708-922">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="2a708-922">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="2a708-924">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-924">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="2a708-926">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-926">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="2a708-927">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-927">Provide additional permissions as needed.</span></span>

<span data-ttu-id="2a708-928">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-928">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="2a708-929">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="2a708-929">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="2a708-930">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="2a708-930">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="2a708-931">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="2a708-931">HTTP/2 support</span></span>

<span data-ttu-id="2a708-932">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="2a708-932">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="2a708-933">进程内</span><span class="sxs-lookup"><span data-stu-id="2a708-933">In-process</span></span>
  * <span data-ttu-id="2a708-934">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2a708-934">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="2a708-935">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="2a708-935">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="2a708-936">进程外</span><span class="sxs-lookup"><span data-stu-id="2a708-936">Out-of-process</span></span>
  * <span data-ttu-id="2a708-937">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2a708-937">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="2a708-938">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="2a708-938">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="2a708-939">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="2a708-939">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2a708-940">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="2a708-940">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="2a708-941">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="2a708-941">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="2a708-942">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="2a708-942">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="2a708-943">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="2a708-943">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="2a708-944">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="2a708-944">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="2a708-945">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="2a708-945">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="2a708-946">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="2a708-946">CORS preflight requests</span></span>

<span data-ttu-id="2a708-947">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-947">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="2a708-948">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-948">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="2a708-949">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="2a708-949">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="2a708-950">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="2a708-950">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="2a708-951">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="2a708-951">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="2a708-952">[应用程序初始化模块](#application-initialization-module)：可以将托管在[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)的应用配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="2a708-952">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="2a708-953">[空闲超时](#idle-timeout)：可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段不超时。</span><span class="sxs-lookup"><span data-stu-id="2a708-953">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="2a708-954">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="2a708-954">Application Initialization Module</span></span>

<span data-ttu-id="2a708-955">适用于托管在进程内和进程外的应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-955">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="2a708-956">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-956">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="2a708-957">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="2a708-957">The request triggers the app to start.</span></span> <span data-ttu-id="2a708-958">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="2a708-958">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="2a708-959">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="2a708-959">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="2a708-960">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="2a708-960">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="2a708-961">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="2a708-961">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="2a708-962">打开“Internet Information Services”>“万维网服务”>“应用程序开发功能”。</span><span class="sxs-lookup"><span data-stu-id="2a708-962">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="2a708-963">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="2a708-963">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="2a708-964">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="2a708-964">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="2a708-965">打开“添加角色和功能向导”。</span><span class="sxs-lookup"><span data-stu-id="2a708-965">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="2a708-966">在“选择角色服务”面板中，打开“应用程序开发”节点 。</span><span class="sxs-lookup"><span data-stu-id="2a708-966">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="2a708-967">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="2a708-967">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="2a708-968">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="2a708-968">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="2a708-969">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="2a708-969">Using IIS Manager:</span></span>

  1. <span data-ttu-id="2a708-970">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="2a708-970">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="2a708-971">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="2a708-971">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="2a708-972">默认的“启动模式”为“按需” 。</span><span class="sxs-lookup"><span data-stu-id="2a708-972">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="2a708-973">将“启动模式”设置为“始终运行”。</span><span class="sxs-lookup"><span data-stu-id="2a708-973">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="2a708-974">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-974">Select **OK**.</span></span>
  1. <span data-ttu-id="2a708-975">打开“连接”面板中的“网站”节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-975">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="2a708-976">右键单击应用，并选择“管理网站”>“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="2a708-976">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="2a708-977">默认的“预加载已启用”设置为“False” 。</span><span class="sxs-lookup"><span data-stu-id="2a708-977">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="2a708-978">将“预加载已启用”设置为“True”。</span><span class="sxs-lookup"><span data-stu-id="2a708-978">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="2a708-979">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-979">Select **OK**.</span></span>

* <span data-ttu-id="2a708-980">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中 ：</span><span class="sxs-lookup"><span data-stu-id="2a708-980">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="2a708-981">空闲超时</span><span class="sxs-lookup"><span data-stu-id="2a708-981">Idle Timeout</span></span>

<span data-ttu-id="2a708-982">仅适用于托管在进程内的应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-982">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="2a708-983">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="2a708-983">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="2a708-984">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="2a708-984">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="2a708-985">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="2a708-985">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="2a708-986">默认的“空闲超时(分钟)”为“20”分钟 。</span><span class="sxs-lookup"><span data-stu-id="2a708-986">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="2a708-987">将“空闲超时(分钟)”设置为“0”（零） 。</span><span class="sxs-lookup"><span data-stu-id="2a708-987">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="2a708-988">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-988">Select **OK**.</span></span>
1. <span data-ttu-id="2a708-989">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="2a708-989">Recycle the worker process.</span></span>

<span data-ttu-id="2a708-990">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="2a708-990">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="2a708-991">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="2a708-991">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="2a708-992">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="2a708-992">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="2a708-993">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="2a708-993">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="2a708-994">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="2a708-994">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="2a708-995">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="2a708-995">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="2a708-996">[应用程序池 \<processModel> 的进程模型设置](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="2a708-996">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="2a708-997">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="2a708-997">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="2a708-998">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="2a708-998">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="2a708-999">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="2a708-999">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="2a708-1000">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="2a708-1000">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="2a708-1001">其他资源</span><span class="sxs-lookup"><span data-stu-id="2a708-1001">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="2a708-1002">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="2a708-1002">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="2a708-1003">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="2a708-1003">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="2a708-1004">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="2a708-1004">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2a708-1005">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1005">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="2a708-1006">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="2a708-1006">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="2a708-1007">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="2a708-1007">Supported operating systems</span></span>

<span data-ttu-id="2a708-1008">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="2a708-1008">The following operating systems are supported:</span></span>

* <span data-ttu-id="2a708-1009">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2a708-1009">Windows 7 or later</span></span>
* <span data-ttu-id="2a708-1010">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2a708-1010">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2a708-1011">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="2a708-1011">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="2a708-1012">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1012">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="2a708-1013">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1013">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="2a708-1014">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1014">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="2a708-1015">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="2a708-1015">Supported platforms</span></span>

<span data-ttu-id="2a708-1016">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-1016">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="2a708-1017">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="2a708-1017">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="2a708-1018">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="2a708-1018">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="2a708-1019">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="2a708-1019">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="2a708-1020">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="2a708-1020">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="2a708-1021">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-1021">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="2a708-1022">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-1022">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="2a708-1023">ASP.NET Core 随附有 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，后者是默认的跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="2a708-1023">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="2a708-1024">使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用在独立于 IIS 工作进程（进程外）和 [Kestrel 服务器](xref:fundamentals/servers/index#kestrel)的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-1024">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="2a708-1025">由于 ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，因此该模块会处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="2a708-1025">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="2a708-1026">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-1026">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="2a708-1027">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="2a708-1027">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2a708-1028">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="2a708-1028">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="2a708-1030">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-1030">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="2a708-1031">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1031">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="2a708-1032">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2a708-1032">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="2a708-1033">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1033">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="2a708-1034">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-1034">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="2a708-1035">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="2a708-1035">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="2a708-1036">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="2a708-1036">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="2a708-1037">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="2a708-1037">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="2a708-1038">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2a708-1038">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="2a708-1039">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="2a708-1039">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="2a708-1040">`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的基础路径和端口来启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="2a708-1040">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="2a708-1041">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="2a708-1041">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="2a708-1042">`CreateDefaultBuilder` 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 方法。</span><span class="sxs-lookup"><span data-stu-id="2a708-1042">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="2a708-1043">`UseIISIntegration` 配置在 localhost IP 地址 (`127.0.0.1`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2a708-1043">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="2a708-1044">如果动态端口为 1234，则 Kestrel 在 `127.0.0.1:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="2a708-1044">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="2a708-1045">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="2a708-1045">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="2a708-1046">Kestrel 的侦听 API</span><span class="sxs-lookup"><span data-stu-id="2a708-1046">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="2a708-1047">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="2a708-1047">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="2a708-1048">使用模块时，不需要调用 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="2a708-1048">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="2a708-1049">如果调用 `UseUrls` 或 `Listen`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="2a708-1049">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="2a708-1050">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1050">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="2a708-1051">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1051">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="2a708-1052">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="2a708-1052">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="2a708-1053">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="2a708-1053">Enable the IISIntegration components</span></span>

<span data-ttu-id="2a708-1054">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="2a708-1054">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="2a708-1055">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1055">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="2a708-1056">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="2a708-1056">IIS options</span></span>

| <span data-ttu-id="2a708-1057">选项</span><span class="sxs-lookup"><span data-stu-id="2a708-1057">Option</span></span>                         | <span data-ttu-id="2a708-1058">默认</span><span class="sxs-lookup"><span data-stu-id="2a708-1058">Default</span></span> | <span data-ttu-id="2a708-1059">设置</span><span class="sxs-lookup"><span data-stu-id="2a708-1059">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="2a708-1060">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1060">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="2a708-1061">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="2a708-1061">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="2a708-1062">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-1062">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="2a708-1063">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1063">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="2a708-1064">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="2a708-1064">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="2a708-1065">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-1065">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="2a708-1066">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="2a708-1066">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="2a708-1067">选项</span><span class="sxs-lookup"><span data-stu-id="2a708-1067">Option</span></span>                         | <span data-ttu-id="2a708-1068">默认</span><span class="sxs-lookup"><span data-stu-id="2a708-1068">Default</span></span> | <span data-ttu-id="2a708-1069">设置</span><span class="sxs-lookup"><span data-stu-id="2a708-1069">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="2a708-1070">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1070">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="2a708-1071">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="2a708-1071">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="2a708-1072">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-1072">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="2a708-1073">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="2a708-1073">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="2a708-1074">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="2a708-1074">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="2a708-1075">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1075">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2a708-1076">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="2a708-1076">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2a708-1077">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="2a708-1077">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="2a708-1078">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-1078">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="2a708-1079">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1079">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="2a708-1080">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="2a708-1080">web.config file</span></span>

<span data-ttu-id="2a708-1081">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1081">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="2a708-1082">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1082">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="2a708-1083">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="2a708-1083">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="2a708-1084">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="2a708-1084">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="2a708-1085">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)  。</span><span class="sxs-lookup"><span data-stu-id="2a708-1085">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="2a708-1086">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="2a708-1086">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="2a708-1087">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="2a708-1087">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="2a708-1088">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="2a708-1088">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="2a708-1089">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="2a708-1089">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="2a708-1090">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="2a708-1090">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="2a708-1091">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="2a708-1091">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="2a708-1092">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1092">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="2a708-1093">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="2a708-1093">web.config file location</span></span>

<span data-ttu-id="2a708-1094">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config 文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="2a708-1094">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="2a708-1095">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="2a708-1095">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="2a708-1096">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1096">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="2a708-1097">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="2a708-1097">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="2a708-1098">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="2a708-1098">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="2a708-1099">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1099">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="2a708-1100">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="2a708-1100">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="2a708-1101">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="2a708-1101">Transform web.config</span></span>

<span data-ttu-id="2a708-1102">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1102">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="2a708-1103">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="2a708-1103">IIS configuration</span></span>

<span data-ttu-id="2a708-1104">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="2a708-1104">**Windows Server operating systems**</span></span>

<span data-ttu-id="2a708-1105">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="2a708-1105">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="2a708-1106">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="2a708-1106">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="2a708-1107">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="2a708-1107">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="2a708-1109">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="2a708-1109">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="2a708-1110">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="2a708-1110">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="2a708-1112">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-1112">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="2a708-1113">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1113">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="2a708-1114">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-1114">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="2a708-1115">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1115">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="2a708-1116">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-1116">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="2a708-1117">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-1117">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="2a708-1118">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1118">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="2a708-1119">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-1119">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="2a708-1120">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1120">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="2a708-1121">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="2a708-1121">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="2a708-1122">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="2a708-1122">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="2a708-1123">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="2a708-1123">**Windows desktop operating systems**</span></span>

<span data-ttu-id="2a708-1124">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1124">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="2a708-1125">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="2a708-1125">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="2a708-1126">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-1126">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="2a708-1127">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-1127">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="2a708-1128">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="2a708-1128">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="2a708-1129">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="2a708-1129">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="2a708-1130">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-1130">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="2a708-1131">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-1131">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="2a708-1132">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1132">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="2a708-1133">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-1133">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="2a708-1134">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1134">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="2a708-1135">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-1135">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="2a708-1136">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-1136">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="2a708-1137">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1137">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="2a708-1138">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="2a708-1138">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="2a708-1139">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1139">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="2a708-1140">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="2a708-1140">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="2a708-1142">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="2a708-1142">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="2a708-1143">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="2a708-1143">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="2a708-1144">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1144">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="2a708-1145">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="2a708-1145">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2a708-1146">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="2a708-1146">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="2a708-1147">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-1147">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="2a708-1148">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="2a708-1148">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="2a708-1149">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1149">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="2a708-1150">下载</span><span class="sxs-lookup"><span data-stu-id="2a708-1150">Download</span></span>

1. <span data-ttu-id="2a708-1151">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="2a708-1151">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="2a708-1152">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-1152">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="2a708-1153">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="2a708-1153">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="2a708-1154">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-1154">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="2a708-1155">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="2a708-1155">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="2a708-1156">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1156">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="2a708-1157">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="2a708-1157">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="2a708-1158">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-1158">Run the installer on the server.</span></span> <span data-ttu-id="2a708-1159">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="2a708-1159">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="2a708-1160">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="2a708-1160">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="2a708-1161">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-1161">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="2a708-1162">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="2a708-1162">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="2a708-1163">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="2a708-1163">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="2a708-1164">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="2a708-1164">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="2a708-1165">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-1165">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="2a708-1166">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="2a708-1166">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="2a708-1167">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-1167">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="2a708-1168">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (applicationHost.config) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="2a708-1168">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="2a708-1169">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-1169">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="2a708-1170">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1170">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="2a708-1171">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2a708-1171">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="2a708-1172">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="2a708-1172">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="2a708-1173">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="2a708-1173">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="2a708-1174">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="2a708-1174">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="2a708-1175">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="2a708-1175">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="2a708-1176">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="2a708-1176">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="2a708-1177">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="2a708-1177">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="2a708-1178">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="2a708-1178">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="2a708-1179">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1179">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="2a708-1180">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="2a708-1180">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="2a708-1181">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="2a708-1181">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="2a708-1182">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-1182">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="2a708-1183">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="2a708-1183">The preferred method is to use WebPI.</span></span> <span data-ttu-id="2a708-1184">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-1184">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="2a708-1185">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="2a708-1185">Create the IIS site</span></span>

1. <span data-ttu-id="2a708-1186">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1186">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="2a708-1187">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="2a708-1187">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="2a708-1188">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1188">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="2a708-1189">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="2a708-1189">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="2a708-1190">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="2a708-1190">Right-click the **Sites** folder.</span></span> <span data-ttu-id="2a708-1191">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1191">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="2a708-1192">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="2a708-1192">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="2a708-1193">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="2a708-1193">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="2a708-1195">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="2a708-1195">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="2a708-1196">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="2a708-1196">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="2a708-1197">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="2a708-1197">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="2a708-1198">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="2a708-1198">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="2a708-1199">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="2a708-1199">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="2a708-1200">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1200">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="2a708-1201">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1201">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="2a708-1202">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1202">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="2a708-1203">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="2a708-1203">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="2a708-1205">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="2a708-1205">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="2a708-1206">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-1206">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="2a708-1207">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="2a708-1207">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="2a708-1208">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-1208">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="2a708-1209">在 IIS 管理器 >“应用程序池”的“操作”侧栏中，选择“设置应用程序池默认设置”或“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1209">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="2a708-1210">找到“启用 32 位应用程序”并将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1210">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="2a708-1211">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-1211">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="2a708-1212">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-1212">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="2a708-1213">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="2a708-1213">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="2a708-1214">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1214">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="2a708-1215">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="2a708-1215">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="2a708-1216">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1216">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="2a708-1217">部署应用</span><span class="sxs-lookup"><span data-stu-id="2a708-1217">Deploy the app</span></span>

<span data-ttu-id="2a708-1218">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="2a708-1218">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="2a708-1219">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="2a708-1219">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="2a708-1220">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="2a708-1220">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="2a708-1221">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1221">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="2a708-1222">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="2a708-1222">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="2a708-1224">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="2a708-1224">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="2a708-1225">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1225">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="2a708-1226">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="2a708-1226">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="2a708-1227">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="2a708-1227">Alternatives to Web Deploy</span></span>

<span data-ttu-id="2a708-1228">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="2a708-1228">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="2a708-1229">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="2a708-1229">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="2a708-1230">浏览网站</span><span class="sxs-lookup"><span data-stu-id="2a708-1230">Browse the website</span></span>

<span data-ttu-id="2a708-1231">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-1231">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="2a708-1232">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="2a708-1232">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="2a708-1233">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="2a708-1233">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="2a708-1235">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="2a708-1235">Locked deployment files</span></span>

<span data-ttu-id="2a708-1236">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="2a708-1236">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="2a708-1237">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1237">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="2a708-1238">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="2a708-1238">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="2a708-1239">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1239">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="2a708-1240">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1240">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="2a708-1241">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1241">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="2a708-1242">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1242">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="2a708-1243">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-1243">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="2a708-1244">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="2a708-1244">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="2a708-1245">数据保护</span><span class="sxs-lookup"><span data-stu-id="2a708-1245">Data protection</span></span>

<span data-ttu-id="2a708-1246">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1246">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="2a708-1247">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1247">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="2a708-1248">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="2a708-1248">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="2a708-1249">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="2a708-1249">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="2a708-1250">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="2a708-1250">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="2a708-1251">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="2a708-1251">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="2a708-1252">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="2a708-1252">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="2a708-1253">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1253">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="2a708-1254">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="2a708-1254">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="2a708-1255">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="2a708-1255">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="2a708-1256">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="2a708-1256">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="2a708-1257">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="2a708-1257">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="2a708-1258">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1258">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="2a708-1259">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="2a708-1259">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="2a708-1260">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="2a708-1260">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="2a708-1261">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="2a708-1261">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="2a708-1262">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="2a708-1262">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="2a708-1263">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="2a708-1263">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="2a708-1264">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="2a708-1264">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="2a708-1265">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="2a708-1265">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="2a708-1266">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1266">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="2a708-1267">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="2a708-1267">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="2a708-1268">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="2a708-1268">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="2a708-1269">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1269">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="2a708-1270">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="2a708-1270">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="2a708-1271">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2a708-1271">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="2a708-1272">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1272">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="2a708-1273">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1273">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="2a708-1274">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1274">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="2a708-1275">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2a708-1275">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="2a708-1276">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2a708-1276">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="2a708-1277">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1277">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="2a708-1278">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="2a708-1278">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="2a708-1279">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1279">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="2a708-1280">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="2a708-1280">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="2a708-1281">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1281">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="2a708-1282">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="2a708-1282">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="2a708-1283">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="2a708-1283">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="2a708-1284">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="2a708-1284">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="2a708-1285">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="2a708-1285">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="2a708-1286">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="2a708-1286">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="2a708-1287">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1287">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="2a708-1288">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="2a708-1288">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="2a708-1289">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1289">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="2a708-1290">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1290">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="2a708-1291">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="2a708-1291">Virtual Directories</span></span>

<span data-ttu-id="2a708-1292">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1292">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="2a708-1293">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1293">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="2a708-1294">子应用程序</span><span class="sxs-lookup"><span data-stu-id="2a708-1294">Sub-applications</span></span>

<span data-ttu-id="2a708-1295">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1295">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="2a708-1296">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="2a708-1296">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="2a708-1297">子应用不应将 ASP.NET Core 模块作为处理程序包含在其中。</span><span class="sxs-lookup"><span data-stu-id="2a708-1297">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="2a708-1298">如果在子应用的 web.config 文件中将该模块添加为处理程序，则在尝试浏览子应用时会收到“500.19 内部服务器错误”，即引用错误的配置文件。</span><span class="sxs-lookup"><span data-stu-id="2a708-1298">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="2a708-1299">以下示例显示 ASP.NET Core 子应用的已发布 web.config 文件：</span><span class="sxs-lookup"><span data-stu-id="2a708-1299">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="2a708-1300">在 ASP.NET Core 应用下托管非 ASP.NET Core 子应用时，需在子应用的 web.config 文件中显式删除继承的处理程序：</span><span class="sxs-lookup"><span data-stu-id="2a708-1300">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

<span data-ttu-id="2a708-1301">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="2a708-1301">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="2a708-1302">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="2a708-1302">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="2a708-1303">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1303">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="2a708-1304">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="2a708-1304">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="2a708-1305">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="2a708-1305">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="2a708-1306">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="2a708-1306">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="2a708-1307">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="2a708-1307">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="2a708-1308">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="2a708-1308">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="2a708-1309">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-1309">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="2a708-1310">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="2a708-1310">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="2a708-1311">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2a708-1311">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="2a708-1312">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1312">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="2a708-1313">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="2a708-1313">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="2a708-1314">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1314">Select **OK**.</span></span>

<span data-ttu-id="2a708-1315">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-1315">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="2a708-1316">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="2a708-1316">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="2a708-1317">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="2a708-1317">Configuration of IIS with web.config</span></span>

<span data-ttu-id="2a708-1318">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="2a708-1318">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="2a708-1319">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="2a708-1319">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="2a708-1320">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="2a708-1320">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="2a708-1321">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="2a708-1321">For more information, see the following topics:</span></span>

* [<span data-ttu-id="2a708-1322">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="2a708-1322">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="2a708-1323">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="2a708-1323">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="2a708-1324">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="2a708-1324">Configuration sections of web.config</span></span>

<span data-ttu-id="2a708-1325">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="2a708-1325">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="2a708-1326">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="2a708-1326">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="2a708-1327">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1327">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="2a708-1328">应用程序池</span><span class="sxs-lookup"><span data-stu-id="2a708-1328">Application Pools</span></span>

<span data-ttu-id="2a708-1329">在服务器上托管多个网站时，建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="2a708-1329">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="2a708-1330">IIS“添加网站”对话框默认执行此配置。</span><span class="sxs-lookup"><span data-stu-id="2a708-1330">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="2a708-1331">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="2a708-1331">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="2a708-1332">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="2a708-1332">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="2a708-1333">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="2a708-1333">Application Pool Identity</span></span>

<span data-ttu-id="2a708-1334">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="2a708-1334">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="2a708-1335">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="2a708-1335">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="2a708-1336">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="2a708-1336">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="2a708-1338">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="2a708-1338">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="2a708-1339">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="2a708-1339">Resources can be secured using this identity.</span></span> <span data-ttu-id="2a708-1340">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="2a708-1340">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="2a708-1341">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="2a708-1341">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="2a708-1342">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="2a708-1342">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="2a708-1343">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1343">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="2a708-1344">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="2a708-1344">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="2a708-1345">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="2a708-1345">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="2a708-1346">在“输入要选择的对象名称”区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="2a708-1346">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="2a708-1347">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="2a708-1347">Select the **Check Names** button.</span></span> <span data-ttu-id="2a708-1348">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="2a708-1348">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="2a708-1349">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="2a708-1349">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="2a708-1350">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="2a708-1350">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="2a708-1351">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="2a708-1351">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="2a708-1353">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="2a708-1353">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="2a708-1355">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-1355">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="2a708-1356">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-1356">Provide additional permissions as needed.</span></span>

<span data-ttu-id="2a708-1357">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="2a708-1357">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="2a708-1358">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="2a708-1358">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="2a708-1359">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="2a708-1359">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="2a708-1360">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="2a708-1360">HTTP/2 support</span></span>

<span data-ttu-id="2a708-1361">满足以下基本要求的进程外部署支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="2a708-1361">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="2a708-1362">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2a708-1362">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="2a708-1363">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="2a708-1363">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="2a708-1364">目标框架：不适用于进程外部署，因为 HTTP/2 连接完全由 IIS 处理。</span><span class="sxs-lookup"><span data-stu-id="2a708-1364">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="2a708-1365">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="2a708-1365">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2a708-1366">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="2a708-1366">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="2a708-1367">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="2a708-1367">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="2a708-1368">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="2a708-1368">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="2a708-1369">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1369">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="2a708-1370">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="2a708-1370">CORS preflight requests</span></span>

<span data-ttu-id="2a708-1371">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-1371">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="2a708-1372">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="2a708-1372">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="2a708-1373">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="2a708-1373">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="2a708-1374">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="2a708-1374">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="2a708-1375">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="2a708-1375">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="2a708-1376">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="2a708-1376">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="2a708-1377">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="2a708-1377">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="2a708-1378">其他资源</span><span class="sxs-lookup"><span data-stu-id="2a708-1378">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="2a708-1379">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="2a708-1379">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="2a708-1380">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="2a708-1380">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="2a708-1381">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="2a708-1381">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
