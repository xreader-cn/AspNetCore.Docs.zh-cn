---
title: 使用 IIS 在 Windows 上托管 ASP.NET Core
author: rick-anderson
description: 了解如何在 Windows Server Internet Information Services (IIS) 上托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/index
ms.openlocfilehash: 72f433ffdc7d08e23fb68fc6ed9903a39959363b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775981"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="e829c-103">使用 IIS 在 Windows 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e829c-103">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e829c-104">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="e829c-104">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="e829c-105">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="e829c-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="e829c-106">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="e829c-106">Supported operating systems</span></span>

<span data-ttu-id="e829c-107">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="e829c-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="e829c-108">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-108">Windows 7 or later</span></span>
* <span data-ttu-id="e829c-109">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-109">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="e829c-110">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="e829c-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="e829c-111">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="e829c-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="e829c-112">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="e829c-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="e829c-113">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="e829c-113">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="e829c-114">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="e829c-114">Supported platforms</span></span>

<span data-ttu-id="e829c-115">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-115">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="e829c-116">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="e829c-116">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="e829c-117">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="e829c-117">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="e829c-118">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="e829c-118">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="e829c-119">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="e829c-119">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="e829c-120">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-120">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="e829c-121">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-121">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="e829c-122">托管模型</span><span class="sxs-lookup"><span data-stu-id="e829c-122">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="e829c-123">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="e829c-123">In-process hosting model</span></span>

<span data-ttu-id="e829c-124">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-124">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="e829c-125">进程内承载相较进程外承载提供更优的性能，因为请求并不通过环回适配器进行代理，环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="e829c-125">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="e829c-126">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="e829c-126">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e829c-127">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="e829c-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="e829c-128">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="e829c-128">Performs app initialization.</span></span>
  * <span data-ttu-id="e829c-129">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="e829c-129">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="e829c-130">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="e829c-130">Calls `Program.Main`.</span></span>
* <span data-ttu-id="e829c-131">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="e829c-131">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="e829c-132">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="e829c-132">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="e829c-133">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="e829c-133">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

<span data-ttu-id="e829c-135">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-135">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="e829c-136">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="e829c-136">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="e829c-137">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="e829c-137">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="e829c-138">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="e829c-138">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="e829c-139">IIS HTTP 服务器处理请求之后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="e829c-139">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e829c-140">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="e829c-140">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e829c-141">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="e829c-141">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="e829c-142">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="e829c-142">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="e829c-143">进程内托管选择使用现有应用，但 [dotnet new](/dotnet/core/tools/dotnet-new) 模板默认使用所有 IIS 和 IIS Express 方案的进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="e829c-143">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="e829c-144">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。  </span><span class="sxs-lookup"><span data-stu-id="e829c-144">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="e829c-145">性能测试表明，与在进程外托管应用并将请求代理到 [Kestrel](xref:fundamentals/servers/kestrel) 服务器相比，在进程中托管 .NET Core 应用可以大大提升请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="e829c-145">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

> [!NOTE]
> <span data-ttu-id="e829c-146">作为单个文件可执行文件发布的应用无法由进程内托管模型加载。</span><span class="sxs-lookup"><span data-stu-id="e829c-146">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="e829c-147">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="e829c-147">Out-of-process hosting model</span></span>

<span data-ttu-id="e829c-148">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="e829c-148">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="e829c-149">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-149">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e829c-150">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="e829c-150">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e829c-151">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="e829c-151">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="e829c-153">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-153">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="e829c-154">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="e829c-154">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="e829c-155">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e829c-155">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="e829c-156">该模块在启动时通过环境变量指定端口，<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="e829c-156">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="e829c-157">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-157">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="e829c-158">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="e829c-158">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="e829c-159">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="e829c-159">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e829c-160">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="e829c-160">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e829c-161">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e829c-161">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="e829c-162">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="e829c-162">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="e829c-163">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-163">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e829c-164">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="e829c-164">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="e829c-165">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="e829c-165">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="e829c-166">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="e829c-166">Enable the IISIntegration components</span></span>

<span data-ttu-id="e829c-167">在 `CreateHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="e829c-167">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="e829c-168">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/generic-host#default-builder-settings>。</span><span class="sxs-lookup"><span data-stu-id="e829c-168">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="e829c-169">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="e829c-169">IIS options</span></span>

<span data-ttu-id="e829c-170">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="e829c-170">**In-process hosting model**</span></span>

<span data-ttu-id="e829c-171">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-171">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="e829c-172">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="e829c-172">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="e829c-173">选项</span><span class="sxs-lookup"><span data-stu-id="e829c-173">Option</span></span>                         | <span data-ttu-id="e829c-174">默认</span><span class="sxs-lookup"><span data-stu-id="e829c-174">Default</span></span> | <span data-ttu-id="e829c-175">设置</span><span class="sxs-lookup"><span data-stu-id="e829c-175">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e829c-176">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="e829c-176">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e829c-177">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="e829c-177">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e829c-178">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-178">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e829c-179">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-179">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e829c-180">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="e829c-180">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="e829c-181">`HttpContext.Request` 和 `HttpContext.Response` 是否允许同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="e829c-181">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="e829c-182">获取或设置 `HttpRequest` 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="e829c-182">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="e829c-183">请注意，IIS 本身有限制 `maxAllowedContentLength`，这一限制将在 `IISServerOptions` 中设置 `MaxRequestBodySize` 之前进行处理。</span><span class="sxs-lookup"><span data-stu-id="e829c-183">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="e829c-184">更改 `MaxRequestBodySize` 不会影响 `maxAllowedContentLength`。</span><span class="sxs-lookup"><span data-stu-id="e829c-184">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="e829c-185">若要增加 `maxAllowedContentLength`，请在 web.config  中添加一个条目，将 `maxAllowedContentLength` 设置为更高值。</span><span class="sxs-lookup"><span data-stu-id="e829c-185">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="e829c-186">有关更多详细信息，请参阅[配置](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。</span><span class="sxs-lookup"><span data-stu-id="e829c-186">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="e829c-187">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="e829c-187">**Out-of-process hosting model**</span></span>

<span data-ttu-id="e829c-188">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-188">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="e829c-189">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="e829c-189">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="e829c-190">选项</span><span class="sxs-lookup"><span data-stu-id="e829c-190">Option</span></span>                         | <span data-ttu-id="e829c-191">默认</span><span class="sxs-lookup"><span data-stu-id="e829c-191">Default</span></span> | <span data-ttu-id="e829c-192">设置</span><span class="sxs-lookup"><span data-stu-id="e829c-192">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e829c-193">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="e829c-193">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e829c-194">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="e829c-194">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e829c-195">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-195">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e829c-196">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="e829c-196">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e829c-197">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="e829c-197">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="e829c-198">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="e829c-198">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e829c-199">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="e829c-199">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e829c-200">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="e829c-200">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="e829c-201">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-201">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="e829c-202">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="e829c-202">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="e829c-203">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="e829c-203">web.config file</span></span>

<span data-ttu-id="e829c-204">web.config  文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="e829c-204">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e829c-205">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-205">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="e829c-206">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="e829c-206">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="e829c-207">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="e829c-207">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="e829c-208">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)    。</span><span class="sxs-lookup"><span data-stu-id="e829c-208">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="e829c-209">如果项目中存在 web.config  文件，则会使用正确的 processPath  和参数  转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="e829c-209">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="e829c-210">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="e829c-210">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="e829c-211">web.config  文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="e829c-211">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="e829c-212">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="e829c-212">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="e829c-213">要防止 Web SDK 转换 web.config  文件，请使用项目文件中的 \<IsTransformWebConfigDisabled>  属性：</span><span class="sxs-lookup"><span data-stu-id="e829c-213">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="e829c-214">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath  和参数  。</span><span class="sxs-lookup"><span data-stu-id="e829c-214">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="e829c-215">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-215">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="e829c-216">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="e829c-216">web.config file location</span></span>

<span data-ttu-id="e829c-217">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config  文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="e829c-217">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="e829c-218">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="e829c-218">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="e829c-219">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-219">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="e829c-220">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json  、\<assembly>.xml  （XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="e829c-220">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="e829c-221">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供  。</span><span class="sxs-lookup"><span data-stu-id="e829c-221">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="e829c-222">如果缺少 web.config  文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-222">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="e829c-223">部署中必须始终存在 web.config  文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config  文件。**</span><span class="sxs-lookup"><span data-stu-id="e829c-223">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="e829c-224">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="e829c-224">Transform web.config</span></span>

<span data-ttu-id="e829c-225">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="e829c-225">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="e829c-226">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="e829c-226">IIS configuration</span></span>

<span data-ttu-id="e829c-227">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="e829c-227">**Windows Server operating systems**</span></span>

<span data-ttu-id="e829c-228">启用 Web 服务器 (IIS)  服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="e829c-228">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="e829c-229">通过“管理”  菜单或“服务器管理器”  中的链接使用“添加角色和功能”  向导。</span><span class="sxs-lookup"><span data-stu-id="e829c-229">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="e829c-230">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框   。</span><span class="sxs-lookup"><span data-stu-id="e829c-230">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="e829c-232">在“功能”  步骤后，为 Web 服务器 (IIS) 加载“角色服务”  步骤。</span><span class="sxs-lookup"><span data-stu-id="e829c-232">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="e829c-233">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="e829c-233">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="e829c-235">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-235">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e829c-236">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-236">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="e829c-237">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-237">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e829c-238">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-238">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e829c-239">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-239">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e829c-240">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-240">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e829c-241">若要启用 WebSocket，请依次展开以下节点：“Web 服务器”   > “应用开发”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-241">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="e829c-242">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-242">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e829c-243">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="e829c-243">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e829c-244">继续执行“确认”步骤，安装 Web 服务器角色和服务  。</span><span class="sxs-lookup"><span data-stu-id="e829c-244">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="e829c-245">安装 Web 服务器 (IIS)  角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="e829c-245">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="e829c-246">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="e829c-246">**Windows desktop operating systems**</span></span>

<span data-ttu-id="e829c-247">启用“IIS 管理控制台”  和“万维网服务”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-247">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e829c-248">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="e829c-248">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="e829c-249">打开“Internet Information Services”  节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-249">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="e829c-250">打开“Web 管理工具”  节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-250">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="e829c-251">选中“IIS 管理控制台”框  。</span><span class="sxs-lookup"><span data-stu-id="e829c-251">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="e829c-252">选中“万维网服务”  框。</span><span class="sxs-lookup"><span data-stu-id="e829c-252">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e829c-253">接受“万维网服务”的默认功能，或自定义 IIS 功能  。</span><span class="sxs-lookup"><span data-stu-id="e829c-253">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="e829c-254">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-254">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e829c-255">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-255">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="e829c-256">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-256">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e829c-257">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-257">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e829c-258">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-258">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e829c-259">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-259">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e829c-260">若要启用 WebSocket，请依次展开以下节点：“万维网服务”   > “应用开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-260">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="e829c-261">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-261">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e829c-262">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="e829c-262">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e829c-263">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="e829c-263">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e829c-265">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="e829c-265">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e829c-266">在托管系统上安装 .NET Core 托管捆绑包  。</span><span class="sxs-lookup"><span data-stu-id="e829c-266">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="e829c-267">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="e829c-267">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e829c-268">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-268">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e829c-269">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="e829c-269">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="e829c-270">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-270">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="e829c-271">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="e829c-271">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="e829c-272">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="e829c-272">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="e829c-273">直接下载（当前版本）</span><span class="sxs-lookup"><span data-stu-id="e829c-273">Direct download (current version)</span></span>

<span data-ttu-id="e829c-274">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="e829c-274">Download the installer using the following link:</span></span>

[<span data-ttu-id="e829c-275">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="e829c-275">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="e829c-276">先前版本的安装程序</span><span class="sxs-lookup"><span data-stu-id="e829c-276">Earlier versions of the installer</span></span>

<span data-ttu-id="e829c-277">若要获取先前版本的安装程序：</span><span class="sxs-lookup"><span data-stu-id="e829c-277">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="e829c-278">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="e829c-278">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="e829c-279">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-279">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="e829c-280">在“运行应用 - 运行时”  列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="e829c-280">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="e829c-281">使用“托管捆绑包”链接下载安装程序  。</span><span class="sxs-lookup"><span data-stu-id="e829c-281">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="e829c-282">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-282">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="e829c-283">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="e829c-283">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="e829c-284">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="e829c-284">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="e829c-285">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-285">Run the installer on the server.</span></span> <span data-ttu-id="e829c-286">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="e829c-286">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="e829c-287">`OPT_NO_ANCM=1` &ndash; 跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="e829c-287">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="e829c-288">`OPT_NO_RUNTIME=1` &ndash; 跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-288">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="e829c-289">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="e829c-289">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e829c-290">`OPT_NO_SHAREDFX=1` &ndash; 跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="e829c-290">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="e829c-291">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="e829c-291">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e829c-292">`OPT_NO_X86=1` &ndash; 跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-292">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="e829c-293">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="e829c-293">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="e829c-294">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-294">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="e829c-295">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 当共享配置 (applicationHost.config  ) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="e829c-295">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="e829c-296">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。 </span><span class="sxs-lookup"><span data-stu-id="e829c-296">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="e829c-297">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="e829c-297">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="e829c-298">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="e829c-298">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="e829c-299">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="e829c-299">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="e829c-300">ASP.NET Core 不采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="e829c-300">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="e829c-301">通过安装新的托管捆绑包升级共享框架后，请重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="e829c-301">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="e829c-302">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="e829c-302">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="e829c-303">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="e829c-303">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="e829c-304">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="e829c-304">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="e829c-305">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-305">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="e829c-306">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="e829c-306">The preferred method is to use WebPI.</span></span> <span data-ttu-id="e829c-307">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-307">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="e829c-308">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="e829c-308">Create the IIS site</span></span>

1. <span data-ttu-id="e829c-309">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-309">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="e829c-310">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="e829c-310">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="e829c-311">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="e829c-311">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="e829c-312">在 IIS 管理器中，打开“连接”  面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-312">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="e829c-313">右键单击“站点”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="e829c-313">Right-click the **Sites** folder.</span></span> <span data-ttu-id="e829c-314">选择上下文菜单中的“添加网站”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-314">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="e829c-315">提供网站名称，并将物理路径设置为应用的部署文件夹   。</span><span class="sxs-lookup"><span data-stu-id="e829c-315">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="e829c-316">提供“绑定”  配置，并通过选择“确定”  创建网站：</span><span class="sxs-lookup"><span data-stu-id="e829c-316">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="e829c-318">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="e829c-318">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e829c-319">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="e829c-319">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="e829c-320">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="e829c-320">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e829c-321">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="e829c-321">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="e829c-322">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="e829c-322">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e829c-323">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="e829c-323">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="e829c-324">在服务器节点下，选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-324">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="e829c-325">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-325">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="e829c-326">在“编辑应用程序池”  窗口中，将“.NET CLR 版本”  设置为“无托管代码”  ：</span><span class="sxs-lookup"><span data-stu-id="e829c-326">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="e829c-328">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-328">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="e829c-329">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-329">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="e829c-330">将“.NET CLR 版本”  设置为“无托管代码”  是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="e829c-330">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="e829c-331">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-331">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="e829c-332">在 IIS 管理器 >“应用程序池”  的“操作”  侧栏中，选择“设置应用程序池默认设置”  或“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-332">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="e829c-333">找到“启用 32 位应用程序”并将值设置为 `False` 。</span><span class="sxs-lookup"><span data-stu-id="e829c-333">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="e829c-334">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-334">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="e829c-335">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-335">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="e829c-336">如果将应用池的默认标识（“进程模型” > “标识”）从 ApplicationPoolIdentity 更改为另一标识，请验证新标识拥有所需的权限，可访问应用的文件夹、数据库和其他所需资源    。</span><span class="sxs-lookup"><span data-stu-id="e829c-336">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="e829c-337">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-337">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="e829c-338">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-338">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="e829c-339">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-339">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="e829c-340">部署应用</span><span class="sxs-lookup"><span data-stu-id="e829c-340">Deploy the app</span></span>

<span data-ttu-id="e829c-341">将应用程序部署到 IIS 物理路径  文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="e829c-341">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="e829c-342">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布  文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="e829c-342">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="e829c-343">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="e829c-343">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="e829c-344">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="e829c-344">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="e829c-345">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”  对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="e829c-345">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="e829c-347">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="e829c-347">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="e829c-348">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="e829c-348">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="e829c-349">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="e829c-349">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="e829c-350">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="e829c-350">Alternatives to Web Deploy</span></span>

<span data-ttu-id="e829c-351">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="e829c-351">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="e829c-352">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="e829c-352">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="e829c-353">浏览网站</span><span class="sxs-lookup"><span data-stu-id="e829c-353">Browse the website</span></span>

<span data-ttu-id="e829c-354">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-354">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="e829c-355">在以下示例中，站点被绑定到端口  `80` 上 `www.mysite.com` 的 IIS 主机名  中。</span><span class="sxs-lookup"><span data-stu-id="e829c-355">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="e829c-356">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="e829c-356">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="e829c-358">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="e829c-358">Locked deployment files</span></span>

<span data-ttu-id="e829c-359">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="e829c-359">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="e829c-360">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-360">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="e829c-361">若要在部署中解除已锁定的文件，请使用以下方法之一  停止应用池：</span><span class="sxs-lookup"><span data-stu-id="e829c-361">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="e829c-362">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="e829c-362">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="e829c-363">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-363">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="e829c-364">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-364">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="e829c-365">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="e829c-365">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="e829c-366">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-366">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="e829c-367">使用 PowerShell 删除 app_offline.html  （需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="e829c-367">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="e829c-368">数据保护</span><span class="sxs-lookup"><span data-stu-id="e829c-368">Data protection</span></span>

<span data-ttu-id="e829c-369">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="e829c-369">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="e829c-370">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="e829c-370">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="e829c-371">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="e829c-371">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="e829c-372">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="e829c-372">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="e829c-373">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="e829c-373">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="e829c-374">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="e829c-374">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="e829c-375">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="e829c-375">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="e829c-376">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="e829c-376">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="e829c-377">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="e829c-377">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="e829c-378">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="e829c-378">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="e829c-379">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="e829c-379">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="e829c-380">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="e829c-380">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="e829c-381">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="e829c-381">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="e829c-382">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="e829c-382">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="e829c-383">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="e829c-383">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="e829c-384">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="e829c-384">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="e829c-385">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="e829c-385">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="e829c-386">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="e829c-386">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="e829c-387">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="e829c-387">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="e829c-388">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="e829c-388">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="e829c-389">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="e829c-389">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="e829c-390">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="e829c-390">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="e829c-391">此设置位于应用池“高级设置”下的“进程模型”部分   。</span><span class="sxs-lookup"><span data-stu-id="e829c-391">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="e829c-392">将“加载用户配置文件”设置为 `True` 。</span><span class="sxs-lookup"><span data-stu-id="e829c-392">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="e829c-393">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="e829c-393">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="e829c-394">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="e829c-394">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="e829c-395">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="e829c-395">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="e829c-396">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="e829c-396">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="e829c-397">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="e829c-397">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="e829c-398">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e829c-398">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="e829c-399">导航到 %windir%/system32/inetsrv/config  文件夹。</span><span class="sxs-lookup"><span data-stu-id="e829c-399">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="e829c-400">打开 applicationHost.config  文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-400">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="e829c-401">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="e829c-401">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="e829c-402">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="e829c-402">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="e829c-403">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="e829c-403">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="e829c-404">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="e829c-404">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="e829c-405">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="e829c-405">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="e829c-406">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="e829c-406">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="e829c-407">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="e829c-407">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="e829c-408">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="e829c-408">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="e829c-409">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="e829c-409">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="e829c-410">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="e829c-410">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="e829c-411">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="e829c-411">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="e829c-412">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="e829c-412">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="e829c-413">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="e829c-413">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="e829c-414">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="e829c-414">Virtual Directories</span></span>

<span data-ttu-id="e829c-415">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="e829c-415">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="e829c-416">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="e829c-416">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="e829c-417">子应用程序</span><span class="sxs-lookup"><span data-stu-id="e829c-417">Sub-applications</span></span>

<span data-ttu-id="e829c-418">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="e829c-418">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="e829c-419">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="e829c-419">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="e829c-420">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="e829c-420">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="e829c-421">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="e829c-421">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="e829c-422">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="e829c-422">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="e829c-423">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-423">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="e829c-424">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="e829c-424">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="e829c-425">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="e829c-425">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="e829c-426">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”  响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="e829c-426">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="e829c-427">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="e829c-427">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="e829c-428">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-428">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="e829c-429">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中   。</span><span class="sxs-lookup"><span data-stu-id="e829c-429">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="e829c-430">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="e829c-430">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="e829c-431">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。 </span><span class="sxs-lookup"><span data-stu-id="e829c-431">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="e829c-432">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。   </span><span class="sxs-lookup"><span data-stu-id="e829c-432">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="e829c-433">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-433">Select **OK**.</span></span>

<span data-ttu-id="e829c-434">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-434">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="e829c-435">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-435">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="e829c-436">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="e829c-436">Configuration of IIS with web.config</span></span>

<span data-ttu-id="e829c-437">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config  的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="e829c-437">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="e829c-438">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="e829c-438">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="e829c-439">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config  文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="e829c-439">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="e829c-440">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="e829c-440">For more information, see the following topics:</span></span>

* [<span data-ttu-id="e829c-441">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="e829c-441">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="e829c-442">若要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”  部分。</span><span class="sxs-lookup"><span data-stu-id="e829c-442">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="e829c-443">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="e829c-443">Configuration sections of web.config</span></span>

<span data-ttu-id="e829c-444">ASP.NET Core 应用不会使用 web.config  中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="e829c-444">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="e829c-445">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-445">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="e829c-446">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="e829c-446">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="e829c-447">应用程序池</span><span class="sxs-lookup"><span data-stu-id="e829c-447">Application Pools</span></span>

<span data-ttu-id="e829c-448">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="e829c-448">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="e829c-449">进程内托管 &ndash; 应用都需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-449">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="e829c-450">进程外托管 &ndash; 建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="e829c-450">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="e829c-451">IIS“添加网站”对话框默认为每应用一个应用池  。</span><span class="sxs-lookup"><span data-stu-id="e829c-451">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="e829c-452">提供了站点名称时，该文本会自动传输到“应用程序池”文本框   。</span><span class="sxs-lookup"><span data-stu-id="e829c-452">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="e829c-453">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-453">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="e829c-454">应用程序池标识</span><span class="sxs-lookup"><span data-stu-id="e829c-454">Application Pool Identity</span></span>

<span data-ttu-id="e829c-455">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="e829c-455">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="e829c-456">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="e829c-456">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="e829c-457">在 IIS 管理控制台中，确保应用池“高级设置”下的“标识”设置为使用 ApplicationPoolIdentity    ：</span><span class="sxs-lookup"><span data-stu-id="e829c-457">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="e829c-459">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="e829c-459">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="e829c-460">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="e829c-460">Resources can be secured using this identity.</span></span> <span data-ttu-id="e829c-461">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="e829c-461">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="e829c-462">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="e829c-462">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="e829c-463">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="e829c-463">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="e829c-464">右键单击该目录，然后选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-464">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="e829c-465">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮    。</span><span class="sxs-lookup"><span data-stu-id="e829c-465">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="e829c-466">选择“位置”按钮，并确保该系统处于选中状态  。</span><span class="sxs-lookup"><span data-stu-id="e829c-466">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="e829c-467">在“输入要选择的对象名称”  区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="e829c-467">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="e829c-468">选择“检查名称”按钮  。</span><span class="sxs-lookup"><span data-stu-id="e829c-468">Select the **Check Names** button.</span></span> <span data-ttu-id="e829c-469">有关 DefaultAppPool  ，请检查使用 IIS AppPool\DefaultAppPool  的名称。</span><span class="sxs-lookup"><span data-stu-id="e829c-469">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="e829c-470">当选择“检查名称”  按钮时，对象名称区域中会显示 DefaultAppPool  的值。</span><span class="sxs-lookup"><span data-stu-id="e829c-470">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="e829c-471">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="e829c-471">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="e829c-472">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="e829c-472">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="e829c-474">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-474">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="e829c-476">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-476">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="e829c-477">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-477">Provide additional permissions as needed.</span></span>

<span data-ttu-id="e829c-478">也可使用 ICACLS  工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-478">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="e829c-479">以 DefaultAppPool  为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="e829c-479">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="e829c-480">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="e829c-480">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e829c-481">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="e829c-481">HTTP/2 support</span></span>

<span data-ttu-id="e829c-482">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="e829c-482">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="e829c-483">进程内</span><span class="sxs-lookup"><span data-stu-id="e829c-483">In-process</span></span>
  * <span data-ttu-id="e829c-484">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-484">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e829c-485">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="e829c-485">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="e829c-486">进程外</span><span class="sxs-lookup"><span data-stu-id="e829c-486">Out-of-process</span></span>
  * <span data-ttu-id="e829c-487">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-487">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e829c-488">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e829c-488">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="e829c-489">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="e829c-489">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e829c-490">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="e829c-490">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="e829c-491">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="e829c-491">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="e829c-492">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-492">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e829c-493">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="e829c-493">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e829c-494">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e829c-494">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="e829c-495">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="e829c-495">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="e829c-496">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="e829c-496">CORS preflight requests</span></span>

<span data-ttu-id="e829c-497">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。 </span><span class="sxs-lookup"><span data-stu-id="e829c-497">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="e829c-498">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-498">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="e829c-499">若要了解如何在 web.config  中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="e829c-499">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="e829c-500">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="e829c-500">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="e829c-501">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="e829c-501">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="e829c-502">[应用程序初始化模块](#application-initialization-module) &ndash;应用的托管[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)可配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="e829c-502">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="e829c-503">[空闲超时](#idle-timeout) &ndash;可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段期间不超时。</span><span class="sxs-lookup"><span data-stu-id="e829c-503">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="e829c-504">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="e829c-504">Application Initialization Module</span></span>

<span data-ttu-id="e829c-505">适用于托管在进程内和进程外的应用。 </span><span class="sxs-lookup"><span data-stu-id="e829c-505">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="e829c-506">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-506">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="e829c-507">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="e829c-507">The request triggers the app to start.</span></span> <span data-ttu-id="e829c-508">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="e829c-508">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="e829c-509">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="e829c-509">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="e829c-510">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="e829c-510">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="e829c-511">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="e829c-511">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="e829c-512">打开“Internet Information Services”  >“万维网服务”  >“应用程序开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-512">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="e829c-513">选中“应用程序初始化”  的复选框。</span><span class="sxs-lookup"><span data-stu-id="e829c-513">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="e829c-514">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="e829c-514">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="e829c-515">打开“添加角色和功能向导”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-515">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="e829c-516">在“选择角色服务”面板中，打开“应用程序开发”节点   。</span><span class="sxs-lookup"><span data-stu-id="e829c-516">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="e829c-517">选中“应用程序初始化”  的复选框。</span><span class="sxs-lookup"><span data-stu-id="e829c-517">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="e829c-518">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="e829c-518">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="e829c-519">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="e829c-519">Using IIS Manager:</span></span>

  1. <span data-ttu-id="e829c-520">在“连接”  面板中选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-520">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="e829c-521">在列表中右键单击应用的应用池，并选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-521">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="e829c-522">默认的“启动模式”为“按需”   。</span><span class="sxs-lookup"><span data-stu-id="e829c-522">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="e829c-523">将“启动模式”  设置为“始终运行”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-523">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="e829c-524">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-524">Select **OK**.</span></span>
  1. <span data-ttu-id="e829c-525">打开“连接”  面板中的“网站”  节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-525">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="e829c-526">右键单击应用，并选择“管理网站”>“高级设置”   。</span><span class="sxs-lookup"><span data-stu-id="e829c-526">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="e829c-527">默认的“预加载已启用”设置为“False”   。</span><span class="sxs-lookup"><span data-stu-id="e829c-527">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="e829c-528">将“预加载已启用”  设置为“True”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-528">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="e829c-529">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-529">Select **OK**.</span></span>

* <span data-ttu-id="e829c-530">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中   ：</span><span class="sxs-lookup"><span data-stu-id="e829c-530">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="e829c-531">空闲超时</span><span class="sxs-lookup"><span data-stu-id="e829c-531">Idle Timeout</span></span>

<span data-ttu-id="e829c-532">仅适用于托管在进程内的应用。 </span><span class="sxs-lookup"><span data-stu-id="e829c-532">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="e829c-533">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="e829c-533">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="e829c-534">在“连接”  面板中选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-534">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="e829c-535">在列表中右键单击应用的应用池，并选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-535">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="e829c-536">默认的“空闲超时(分钟)”为“20”分钟   。</span><span class="sxs-lookup"><span data-stu-id="e829c-536">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="e829c-537">将“空闲超时(分钟)”设置为“0”（零）   。</span><span class="sxs-lookup"><span data-stu-id="e829c-537">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="e829c-538">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-538">Select **OK**.</span></span>
1. <span data-ttu-id="e829c-539">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="e829c-539">Recycle the worker process.</span></span>

<span data-ttu-id="e829c-540">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="e829c-540">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="e829c-541">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="e829c-541">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="e829c-542">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="e829c-542">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="e829c-543">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="e829c-543">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="e829c-544">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="e829c-544">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="e829c-545">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="e829c-545">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="e829c-546">[应用程序池的进程模型设置 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="e829c-546">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="e829c-547">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="e829c-547">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="e829c-548">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="e829c-548">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="e829c-549">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="e829c-549">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="e829c-550">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="e829c-550">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="e829c-551">其他资源</span><span class="sxs-lookup"><span data-stu-id="e829c-551">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="e829c-552">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="e829c-552">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="e829c-553">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="e829c-553">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="e829c-554">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e829c-554">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e829c-555">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="e829c-555">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="e829c-556">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="e829c-556">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="e829c-557">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="e829c-557">Supported operating systems</span></span>

<span data-ttu-id="e829c-558">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="e829c-558">The following operating systems are supported:</span></span>

* <span data-ttu-id="e829c-559">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-559">Windows 7 or later</span></span>
* <span data-ttu-id="e829c-560">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-560">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="e829c-561">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="e829c-561">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="e829c-562">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="e829c-562">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="e829c-563">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="e829c-563">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="e829c-564">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="e829c-564">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="e829c-565">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="e829c-565">Supported platforms</span></span>

<span data-ttu-id="e829c-566">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-566">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="e829c-567">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="e829c-567">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="e829c-568">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="e829c-568">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="e829c-569">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="e829c-569">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="e829c-570">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="e829c-570">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="e829c-571">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-571">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="e829c-572">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-572">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="e829c-573">托管模型</span><span class="sxs-lookup"><span data-stu-id="e829c-573">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="e829c-574">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="e829c-574">In-process hosting model</span></span>

<span data-ttu-id="e829c-575">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-575">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="e829c-576">进程内承载相较进程外承载提供更优的性能，因为请求并不通过环回适配器进行代理，环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="e829c-576">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="e829c-577">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="e829c-577">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e829c-578">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="e829c-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="e829c-579">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="e829c-579">Performs app initialization.</span></span>
  * <span data-ttu-id="e829c-580">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="e829c-580">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="e829c-581">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="e829c-581">Calls `Program.Main`.</span></span>
* <span data-ttu-id="e829c-582">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="e829c-582">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="e829c-583">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="e829c-583">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="e829c-584">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="e829c-584">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

<span data-ttu-id="e829c-586">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-586">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="e829c-587">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="e829c-587">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="e829c-588">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="e829c-588">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="e829c-589">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="e829c-589">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="e829c-590">IIS HTTP 服务器处理请求之后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="e829c-590">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e829c-591">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="e829c-591">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e829c-592">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="e829c-592">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="e829c-593">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="e829c-593">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="e829c-594">进程内托管选择使用现有应用，但 [dotnet new](/dotnet/core/tools/dotnet-new) 模板默认使用所有 IIS 和 IIS Express 方案的进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="e829c-594">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="e829c-595">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。  </span><span class="sxs-lookup"><span data-stu-id="e829c-595">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="e829c-596">性能测试表明，与在进程外托管应用并将请求代理到 [Kestrel](xref:fundamentals/servers/kestrel) 服务器相比，在进程中托管 .NET Core 应用可以大大提升请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="e829c-596">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="e829c-597">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="e829c-597">Out-of-process hosting model</span></span>

<span data-ttu-id="e829c-598">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="e829c-598">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="e829c-599">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-599">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e829c-600">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="e829c-600">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e829c-601">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="e829c-601">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="e829c-603">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-603">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="e829c-604">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="e829c-604">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="e829c-605">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e829c-605">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="e829c-606">该模块在启动时通过环境变量指定端口，<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="e829c-606">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="e829c-607">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-607">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="e829c-608">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="e829c-608">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="e829c-609">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="e829c-609">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e829c-610">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="e829c-610">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e829c-611">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e829c-611">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="e829c-612">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="e829c-612">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="e829c-613">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-613">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e829c-614">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="e829c-614">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="e829c-615">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="e829c-615">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="e829c-616">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="e829c-616">Enable the IISIntegration components</span></span>

<span data-ttu-id="e829c-617">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="e829c-617">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="e829c-618">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="e829c-618">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="e829c-619">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="e829c-619">IIS options</span></span>

<span data-ttu-id="e829c-620">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="e829c-620">**In-process hosting model**</span></span>

<span data-ttu-id="e829c-621">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-621">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="e829c-622">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="e829c-622">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="e829c-623">选项</span><span class="sxs-lookup"><span data-stu-id="e829c-623">Option</span></span>                         | <span data-ttu-id="e829c-624">默认</span><span class="sxs-lookup"><span data-stu-id="e829c-624">Default</span></span> | <span data-ttu-id="e829c-625">设置</span><span class="sxs-lookup"><span data-stu-id="e829c-625">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e829c-626">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="e829c-626">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e829c-627">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="e829c-627">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e829c-628">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-628">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e829c-629">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-629">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e829c-630">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="e829c-630">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="e829c-631">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="e829c-631">**Out-of-process hosting model**</span></span>

<span data-ttu-id="e829c-632">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-632">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="e829c-633">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="e829c-633">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="e829c-634">选项</span><span class="sxs-lookup"><span data-stu-id="e829c-634">Option</span></span>                         | <span data-ttu-id="e829c-635">默认</span><span class="sxs-lookup"><span data-stu-id="e829c-635">Default</span></span> | <span data-ttu-id="e829c-636">设置</span><span class="sxs-lookup"><span data-stu-id="e829c-636">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e829c-637">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="e829c-637">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e829c-638">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="e829c-638">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e829c-639">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-639">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e829c-640">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="e829c-640">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e829c-641">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="e829c-641">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="e829c-642">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="e829c-642">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e829c-643">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="e829c-643">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e829c-644">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="e829c-644">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="e829c-645">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-645">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="e829c-646">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="e829c-646">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="e829c-647">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="e829c-647">web.config file</span></span>

<span data-ttu-id="e829c-648">web.config  文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="e829c-648">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e829c-649">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-649">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="e829c-650">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="e829c-650">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="e829c-651">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="e829c-651">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="e829c-652">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)    。</span><span class="sxs-lookup"><span data-stu-id="e829c-652">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="e829c-653">如果项目中存在 web.config  文件，则会使用正确的 processPath  和参数  转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="e829c-653">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="e829c-654">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="e829c-654">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="e829c-655">web.config  文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="e829c-655">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="e829c-656">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="e829c-656">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="e829c-657">要防止 Web SDK 转换 web.config  文件，请使用项目文件中的 \<IsTransformWebConfigDisabled>  属性：</span><span class="sxs-lookup"><span data-stu-id="e829c-657">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="e829c-658">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath  和参数  。</span><span class="sxs-lookup"><span data-stu-id="e829c-658">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="e829c-659">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-659">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="e829c-660">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="e829c-660">web.config file location</span></span>

<span data-ttu-id="e829c-661">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config  文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="e829c-661">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="e829c-662">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="e829c-662">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="e829c-663">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-663">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="e829c-664">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json  、\<assembly>.xml  （XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="e829c-664">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="e829c-665">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供  。</span><span class="sxs-lookup"><span data-stu-id="e829c-665">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="e829c-666">如果缺少 web.config  文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-666">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="e829c-667">部署中必须始终存在 web.config  文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config  文件。**</span><span class="sxs-lookup"><span data-stu-id="e829c-667">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="e829c-668">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="e829c-668">Transform web.config</span></span>

<span data-ttu-id="e829c-669">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="e829c-669">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="e829c-670">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="e829c-670">IIS configuration</span></span>

<span data-ttu-id="e829c-671">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="e829c-671">**Windows Server operating systems**</span></span>

<span data-ttu-id="e829c-672">启用 Web 服务器 (IIS)  服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="e829c-672">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="e829c-673">通过“管理”  菜单或“服务器管理器”  中的链接使用“添加角色和功能”  向导。</span><span class="sxs-lookup"><span data-stu-id="e829c-673">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="e829c-674">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框   。</span><span class="sxs-lookup"><span data-stu-id="e829c-674">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="e829c-676">在“功能”  步骤后，为 Web 服务器 (IIS) 加载“角色服务”  步骤。</span><span class="sxs-lookup"><span data-stu-id="e829c-676">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="e829c-677">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="e829c-677">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="e829c-679">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-679">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e829c-680">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-680">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="e829c-681">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-681">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e829c-682">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-682">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e829c-683">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-683">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e829c-684">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-684">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e829c-685">若要启用 WebSocket，请依次展开以下节点：“Web 服务器”   > “应用开发”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-685">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="e829c-686">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-686">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e829c-687">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="e829c-687">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e829c-688">继续执行“确认”步骤，安装 Web 服务器角色和服务  。</span><span class="sxs-lookup"><span data-stu-id="e829c-688">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="e829c-689">安装 Web 服务器 (IIS)  角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="e829c-689">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="e829c-690">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="e829c-690">**Windows desktop operating systems**</span></span>

<span data-ttu-id="e829c-691">启用“IIS 管理控制台”  和“万维网服务”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-691">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e829c-692">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="e829c-692">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="e829c-693">打开“Internet Information Services”  节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-693">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="e829c-694">打开“Web 管理工具”  节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-694">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="e829c-695">选中“IIS 管理控制台”框  。</span><span class="sxs-lookup"><span data-stu-id="e829c-695">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="e829c-696">选中“万维网服务”  框。</span><span class="sxs-lookup"><span data-stu-id="e829c-696">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e829c-697">接受“万维网服务”的默认功能，或自定义 IIS 功能  。</span><span class="sxs-lookup"><span data-stu-id="e829c-697">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="e829c-698">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-698">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e829c-699">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-699">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="e829c-700">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-700">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e829c-701">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-701">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e829c-702">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-702">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e829c-703">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-703">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e829c-704">若要启用 WebSocket，请依次展开以下节点：“万维网服务”   > “应用开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-704">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="e829c-705">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-705">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e829c-706">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="e829c-706">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e829c-707">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="e829c-707">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e829c-709">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="e829c-709">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e829c-710">在托管系统上安装 .NET Core 托管捆绑包  。</span><span class="sxs-lookup"><span data-stu-id="e829c-710">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="e829c-711">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="e829c-711">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e829c-712">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-712">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e829c-713">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="e829c-713">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="e829c-714">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-714">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="e829c-715">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="e829c-715">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="e829c-716">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="e829c-716">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="e829c-717">下载</span><span class="sxs-lookup"><span data-stu-id="e829c-717">Download</span></span>

1. <span data-ttu-id="e829c-718">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="e829c-718">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="e829c-719">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-719">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="e829c-720">在“运行应用 - 运行时”  列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="e829c-720">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="e829c-721">使用“托管捆绑包”链接下载安装程序  。</span><span class="sxs-lookup"><span data-stu-id="e829c-721">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="e829c-722">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-722">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="e829c-723">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="e829c-723">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="e829c-724">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="e829c-724">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="e829c-725">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-725">Run the installer on the server.</span></span> <span data-ttu-id="e829c-726">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="e829c-726">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="e829c-727">`OPT_NO_ANCM=1` &ndash; 跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="e829c-727">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="e829c-728">`OPT_NO_RUNTIME=1` &ndash; 跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-728">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="e829c-729">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="e829c-729">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e829c-730">`OPT_NO_SHAREDFX=1` &ndash; 跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="e829c-730">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="e829c-731">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="e829c-731">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e829c-732">`OPT_NO_X86=1` &ndash; 跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-732">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="e829c-733">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="e829c-733">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="e829c-734">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-734">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="e829c-735">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 当共享配置 (applicationHost.config  ) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="e829c-735">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="e829c-736">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。 </span><span class="sxs-lookup"><span data-stu-id="e829c-736">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="e829c-737">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="e829c-737">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="e829c-738">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="e829c-738">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="e829c-739">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="e829c-739">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="e829c-740">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="e829c-740">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="e829c-741">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="e829c-741">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="e829c-742">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="e829c-742">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="e829c-743">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="e829c-743">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="e829c-744">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="e829c-744">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="e829c-745">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="e829c-745">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="e829c-746">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="e829c-746">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="e829c-747">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="e829c-747">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="e829c-748">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="e829c-748">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="e829c-749">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-749">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="e829c-750">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="e829c-750">The preferred method is to use WebPI.</span></span> <span data-ttu-id="e829c-751">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-751">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="e829c-752">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="e829c-752">Create the IIS site</span></span>

1. <span data-ttu-id="e829c-753">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-753">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="e829c-754">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="e829c-754">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="e829c-755">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="e829c-755">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="e829c-756">在 IIS 管理器中，打开“连接”  面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-756">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="e829c-757">右键单击“站点”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="e829c-757">Right-click the **Sites** folder.</span></span> <span data-ttu-id="e829c-758">选择上下文菜单中的“添加网站”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-758">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="e829c-759">提供网站名称，并将物理路径设置为应用的部署文件夹   。</span><span class="sxs-lookup"><span data-stu-id="e829c-759">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="e829c-760">提供“绑定”  配置，并通过选择“确定”  创建网站：</span><span class="sxs-lookup"><span data-stu-id="e829c-760">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="e829c-762">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="e829c-762">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e829c-763">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="e829c-763">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="e829c-764">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="e829c-764">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e829c-765">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="e829c-765">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="e829c-766">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="e829c-766">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e829c-767">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="e829c-767">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="e829c-768">在服务器节点下，选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-768">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="e829c-769">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-769">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="e829c-770">在“编辑应用程序池”  窗口中，将“.NET CLR 版本”  设置为“无托管代码”  ：</span><span class="sxs-lookup"><span data-stu-id="e829c-770">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="e829c-772">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-772">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="e829c-773">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-773">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="e829c-774">将“.NET CLR 版本”  设置为“无托管代码”  是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="e829c-774">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="e829c-775">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-775">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="e829c-776">在 IIS 管理器 >“应用程序池”  的“操作”  侧栏中，选择“设置应用程序池默认设置”  或“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-776">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="e829c-777">找到“启用 32 位应用程序”并将值设置为 `False` 。</span><span class="sxs-lookup"><span data-stu-id="e829c-777">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="e829c-778">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-778">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="e829c-779">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-779">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="e829c-780">如果将应用池的默认标识（“进程模型” > “标识”）从 ApplicationPoolIdentity 更改为另一标识，请验证新标识拥有所需的权限，可访问应用的文件夹、数据库和其他所需资源    。</span><span class="sxs-lookup"><span data-stu-id="e829c-780">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="e829c-781">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-781">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="e829c-782">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-782">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="e829c-783">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-783">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="e829c-784">部署应用</span><span class="sxs-lookup"><span data-stu-id="e829c-784">Deploy the app</span></span>

<span data-ttu-id="e829c-785">将应用程序部署到 IIS 物理路径  文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="e829c-785">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="e829c-786">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布  文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="e829c-786">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="e829c-787">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="e829c-787">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="e829c-788">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="e829c-788">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="e829c-789">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”  对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="e829c-789">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="e829c-791">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="e829c-791">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="e829c-792">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="e829c-792">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="e829c-793">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="e829c-793">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="e829c-794">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="e829c-794">Alternatives to Web Deploy</span></span>

<span data-ttu-id="e829c-795">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="e829c-795">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="e829c-796">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="e829c-796">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="e829c-797">浏览网站</span><span class="sxs-lookup"><span data-stu-id="e829c-797">Browse the website</span></span>

<span data-ttu-id="e829c-798">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-798">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="e829c-799">在以下示例中，站点被绑定到端口  `80` 上 `www.mysite.com` 的 IIS 主机名  中。</span><span class="sxs-lookup"><span data-stu-id="e829c-799">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="e829c-800">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="e829c-800">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="e829c-802">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="e829c-802">Locked deployment files</span></span>

<span data-ttu-id="e829c-803">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="e829c-803">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="e829c-804">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-804">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="e829c-805">若要在部署中解除已锁定的文件，请使用以下方法之一  停止应用池：</span><span class="sxs-lookup"><span data-stu-id="e829c-805">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="e829c-806">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="e829c-806">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="e829c-807">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-807">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="e829c-808">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-808">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="e829c-809">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="e829c-809">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="e829c-810">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-810">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="e829c-811">使用 PowerShell 删除 app_offline.html  （需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="e829c-811">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="e829c-812">数据保护</span><span class="sxs-lookup"><span data-stu-id="e829c-812">Data protection</span></span>

<span data-ttu-id="e829c-813">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="e829c-813">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="e829c-814">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="e829c-814">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="e829c-815">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="e829c-815">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="e829c-816">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="e829c-816">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="e829c-817">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="e829c-817">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="e829c-818">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="e829c-818">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="e829c-819">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="e829c-819">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="e829c-820">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="e829c-820">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="e829c-821">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="e829c-821">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="e829c-822">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="e829c-822">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="e829c-823">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="e829c-823">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="e829c-824">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="e829c-824">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="e829c-825">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="e829c-825">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="e829c-826">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="e829c-826">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="e829c-827">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="e829c-827">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="e829c-828">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="e829c-828">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="e829c-829">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="e829c-829">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="e829c-830">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="e829c-830">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="e829c-831">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="e829c-831">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="e829c-832">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="e829c-832">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="e829c-833">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="e829c-833">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="e829c-834">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="e829c-834">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="e829c-835">此设置位于应用池“高级设置”下的“进程模型”部分   。</span><span class="sxs-lookup"><span data-stu-id="e829c-835">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="e829c-836">将“加载用户配置文件”设置为 `True` 。</span><span class="sxs-lookup"><span data-stu-id="e829c-836">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="e829c-837">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="e829c-837">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="e829c-838">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="e829c-838">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="e829c-839">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="e829c-839">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="e829c-840">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="e829c-840">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="e829c-841">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="e829c-841">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="e829c-842">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e829c-842">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="e829c-843">导航到 %windir%/system32/inetsrv/config  文件夹。</span><span class="sxs-lookup"><span data-stu-id="e829c-843">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="e829c-844">打开 applicationHost.config  文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-844">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="e829c-845">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="e829c-845">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="e829c-846">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="e829c-846">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="e829c-847">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="e829c-847">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="e829c-848">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="e829c-848">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="e829c-849">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="e829c-849">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="e829c-850">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="e829c-850">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="e829c-851">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="e829c-851">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="e829c-852">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="e829c-852">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="e829c-853">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="e829c-853">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="e829c-854">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="e829c-854">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="e829c-855">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="e829c-855">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="e829c-856">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="e829c-856">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="e829c-857">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="e829c-857">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="e829c-858">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="e829c-858">Virtual Directories</span></span>

<span data-ttu-id="e829c-859">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="e829c-859">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="e829c-860">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="e829c-860">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="e829c-861">子应用程序</span><span class="sxs-lookup"><span data-stu-id="e829c-861">Sub-applications</span></span>

<span data-ttu-id="e829c-862">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="e829c-862">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="e829c-863">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="e829c-863">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="e829c-864">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="e829c-864">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="e829c-865">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="e829c-865">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="e829c-866">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="e829c-866">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="e829c-867">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-867">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="e829c-868">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="e829c-868">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="e829c-869">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="e829c-869">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="e829c-870">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”  响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="e829c-870">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="e829c-871">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="e829c-871">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="e829c-872">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-872">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="e829c-873">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中   。</span><span class="sxs-lookup"><span data-stu-id="e829c-873">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="e829c-874">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="e829c-874">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="e829c-875">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。 </span><span class="sxs-lookup"><span data-stu-id="e829c-875">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="e829c-876">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。   </span><span class="sxs-lookup"><span data-stu-id="e829c-876">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="e829c-877">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-877">Select **OK**.</span></span>

<span data-ttu-id="e829c-878">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-878">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="e829c-879">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-879">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="e829c-880">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="e829c-880">Configuration of IIS with web.config</span></span>

<span data-ttu-id="e829c-881">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config  的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="e829c-881">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="e829c-882">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="e829c-882">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="e829c-883">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config  文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="e829c-883">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="e829c-884">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="e829c-884">For more information, see the following topics:</span></span>

* [<span data-ttu-id="e829c-885">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="e829c-885">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="e829c-886">若要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”  部分。</span><span class="sxs-lookup"><span data-stu-id="e829c-886">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="e829c-887">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="e829c-887">Configuration sections of web.config</span></span>

<span data-ttu-id="e829c-888">ASP.NET Core 应用不会使用 web.config  中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="e829c-888">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="e829c-889">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-889">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="e829c-890">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="e829c-890">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="e829c-891">应用程序池</span><span class="sxs-lookup"><span data-stu-id="e829c-891">Application Pools</span></span>

<span data-ttu-id="e829c-892">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="e829c-892">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="e829c-893">进程内托管 &ndash; 应用都需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-893">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="e829c-894">进程外托管 &ndash; 建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="e829c-894">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="e829c-895">IIS“添加网站”对话框默认为每应用一个应用池  。</span><span class="sxs-lookup"><span data-stu-id="e829c-895">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="e829c-896">提供了站点名称时，该文本会自动传输到“应用程序池”文本框   。</span><span class="sxs-lookup"><span data-stu-id="e829c-896">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="e829c-897">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-897">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="e829c-898">应用程序池标识</span><span class="sxs-lookup"><span data-stu-id="e829c-898">Application Pool Identity</span></span>

<span data-ttu-id="e829c-899">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="e829c-899">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="e829c-900">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="e829c-900">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="e829c-901">在 IIS 管理控制台中，确保应用池“高级设置”下的“标识”设置为使用 ApplicationPoolIdentity    ：</span><span class="sxs-lookup"><span data-stu-id="e829c-901">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="e829c-903">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="e829c-903">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="e829c-904">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="e829c-904">Resources can be secured using this identity.</span></span> <span data-ttu-id="e829c-905">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="e829c-905">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="e829c-906">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="e829c-906">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="e829c-907">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="e829c-907">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="e829c-908">右键单击该目录，然后选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-908">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="e829c-909">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮    。</span><span class="sxs-lookup"><span data-stu-id="e829c-909">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="e829c-910">选择“位置”按钮，并确保该系统处于选中状态  。</span><span class="sxs-lookup"><span data-stu-id="e829c-910">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="e829c-911">在“输入要选择的对象名称”  区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="e829c-911">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="e829c-912">选择“检查名称”按钮  。</span><span class="sxs-lookup"><span data-stu-id="e829c-912">Select the **Check Names** button.</span></span> <span data-ttu-id="e829c-913">有关 DefaultAppPool  ，请检查使用 IIS AppPool\DefaultAppPool  的名称。</span><span class="sxs-lookup"><span data-stu-id="e829c-913">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="e829c-914">当选择“检查名称”  按钮时，对象名称区域中会显示 DefaultAppPool  的值。</span><span class="sxs-lookup"><span data-stu-id="e829c-914">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="e829c-915">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="e829c-915">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="e829c-916">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="e829c-916">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="e829c-918">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-918">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="e829c-920">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-920">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="e829c-921">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-921">Provide additional permissions as needed.</span></span>

<span data-ttu-id="e829c-922">也可使用 ICACLS  工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-922">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="e829c-923">以 DefaultAppPool  为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="e829c-923">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="e829c-924">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="e829c-924">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e829c-925">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="e829c-925">HTTP/2 support</span></span>

<span data-ttu-id="e829c-926">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="e829c-926">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="e829c-927">进程内</span><span class="sxs-lookup"><span data-stu-id="e829c-927">In-process</span></span>
  * <span data-ttu-id="e829c-928">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-928">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e829c-929">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="e829c-929">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="e829c-930">进程外</span><span class="sxs-lookup"><span data-stu-id="e829c-930">Out-of-process</span></span>
  * <span data-ttu-id="e829c-931">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-931">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e829c-932">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e829c-932">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="e829c-933">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="e829c-933">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e829c-934">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="e829c-934">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="e829c-935">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="e829c-935">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="e829c-936">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-936">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e829c-937">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="e829c-937">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e829c-938">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e829c-938">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="e829c-939">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="e829c-939">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="e829c-940">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="e829c-940">CORS preflight requests</span></span>

<span data-ttu-id="e829c-941">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。 </span><span class="sxs-lookup"><span data-stu-id="e829c-941">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="e829c-942">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-942">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="e829c-943">若要了解如何在 web.config  中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="e829c-943">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="e829c-944">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="e829c-944">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="e829c-945">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="e829c-945">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="e829c-946">[应用程序初始化模块](#application-initialization-module) &ndash;应用的托管[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)可配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="e829c-946">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="e829c-947">[空闲超时](#idle-timeout) &ndash;可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段期间不超时。</span><span class="sxs-lookup"><span data-stu-id="e829c-947">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="e829c-948">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="e829c-948">Application Initialization Module</span></span>

<span data-ttu-id="e829c-949">适用于托管在进程内和进程外的应用。 </span><span class="sxs-lookup"><span data-stu-id="e829c-949">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="e829c-950">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-950">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="e829c-951">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="e829c-951">The request triggers the app to start.</span></span> <span data-ttu-id="e829c-952">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="e829c-952">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="e829c-953">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="e829c-953">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="e829c-954">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="e829c-954">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="e829c-955">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="e829c-955">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="e829c-956">打开“Internet Information Services”  >“万维网服务”  >“应用程序开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-956">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="e829c-957">选中“应用程序初始化”  的复选框。</span><span class="sxs-lookup"><span data-stu-id="e829c-957">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="e829c-958">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="e829c-958">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="e829c-959">打开“添加角色和功能向导”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-959">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="e829c-960">在“选择角色服务”面板中，打开“应用程序开发”节点   。</span><span class="sxs-lookup"><span data-stu-id="e829c-960">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="e829c-961">选中“应用程序初始化”  的复选框。</span><span class="sxs-lookup"><span data-stu-id="e829c-961">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="e829c-962">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="e829c-962">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="e829c-963">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="e829c-963">Using IIS Manager:</span></span>

  1. <span data-ttu-id="e829c-964">在“连接”  面板中选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-964">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="e829c-965">在列表中右键单击应用的应用池，并选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-965">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="e829c-966">默认的“启动模式”为“按需”   。</span><span class="sxs-lookup"><span data-stu-id="e829c-966">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="e829c-967">将“启动模式”  设置为“始终运行”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-967">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="e829c-968">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-968">Select **OK**.</span></span>
  1. <span data-ttu-id="e829c-969">打开“连接”  面板中的“网站”  节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-969">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="e829c-970">右键单击应用，并选择“管理网站”>“高级设置”   。</span><span class="sxs-lookup"><span data-stu-id="e829c-970">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="e829c-971">默认的“预加载已启用”设置为“False”   。</span><span class="sxs-lookup"><span data-stu-id="e829c-971">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="e829c-972">将“预加载已启用”  设置为“True”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-972">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="e829c-973">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-973">Select **OK**.</span></span>

* <span data-ttu-id="e829c-974">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中   ：</span><span class="sxs-lookup"><span data-stu-id="e829c-974">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="e829c-975">空闲超时</span><span class="sxs-lookup"><span data-stu-id="e829c-975">Idle Timeout</span></span>

<span data-ttu-id="e829c-976">仅适用于托管在进程内的应用。 </span><span class="sxs-lookup"><span data-stu-id="e829c-976">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="e829c-977">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="e829c-977">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="e829c-978">在“连接”  面板中选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-978">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="e829c-979">在列表中右键单击应用的应用池，并选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-979">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="e829c-980">默认的“空闲超时(分钟)”为“20”分钟   。</span><span class="sxs-lookup"><span data-stu-id="e829c-980">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="e829c-981">将“空闲超时(分钟)”设置为“0”（零）   。</span><span class="sxs-lookup"><span data-stu-id="e829c-981">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="e829c-982">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-982">Select **OK**.</span></span>
1. <span data-ttu-id="e829c-983">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="e829c-983">Recycle the worker process.</span></span>

<span data-ttu-id="e829c-984">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="e829c-984">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="e829c-985">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="e829c-985">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="e829c-986">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="e829c-986">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="e829c-987">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="e829c-987">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="e829c-988">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="e829c-988">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="e829c-989">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="e829c-989">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="e829c-990">[应用程序池的进程模型设置 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="e829c-990">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="e829c-991">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="e829c-991">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="e829c-992">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="e829c-992">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="e829c-993">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="e829c-993">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="e829c-994">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="e829c-994">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="e829c-995">其他资源</span><span class="sxs-lookup"><span data-stu-id="e829c-995">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="e829c-996">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="e829c-996">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="e829c-997">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="e829c-997">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="e829c-998">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e829c-998">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e829c-999">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="e829c-999">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="e829c-1000">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="e829c-1000">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="e829c-1001">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="e829c-1001">Supported operating systems</span></span>

<span data-ttu-id="e829c-1002">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="e829c-1002">The following operating systems are supported:</span></span>

* <span data-ttu-id="e829c-1003">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-1003">Windows 7 or later</span></span>
* <span data-ttu-id="e829c-1004">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-1004">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="e829c-1005">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="e829c-1005">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="e829c-1006">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1006">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="e829c-1007">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="e829c-1007">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="e829c-1008">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="e829c-1008">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="e829c-1009">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="e829c-1009">Supported platforms</span></span>

<span data-ttu-id="e829c-1010">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-1010">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="e829c-1011">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="e829c-1011">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="e829c-1012">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="e829c-1012">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="e829c-1013">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="e829c-1013">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="e829c-1014">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="e829c-1014">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="e829c-1015">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-1015">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="e829c-1016">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-1016">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="e829c-1017">ASP.NET Core 随附有 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，后者是默认的跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="e829c-1017">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="e829c-1018">使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用在独立于 IIS 工作进程（进程外）  和 [Kestrel 服务器](xref:fundamentals/servers/index#kestrel)的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-1018">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="e829c-1019">由于 ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，因此该模块会处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="e829c-1019">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="e829c-1020">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-1020">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e829c-1021">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="e829c-1021">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e829c-1022">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="e829c-1022">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="e829c-1024">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-1024">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="e829c-1025">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1025">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="e829c-1026">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e829c-1026">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="e829c-1027">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="e829c-1027">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="e829c-1028">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-1028">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="e829c-1029">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="e829c-1029">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="e829c-1030">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="e829c-1030">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e829c-1031">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="e829c-1031">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e829c-1032">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e829c-1032">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="e829c-1033">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="e829c-1033">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="e829c-1034">`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的基础路径和端口来启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="e829c-1034">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="e829c-1035">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="e829c-1035">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="e829c-1036">`CreateDefaultBuilder` 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 方法。</span><span class="sxs-lookup"><span data-stu-id="e829c-1036">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="e829c-1037">`UseIISIntegration` 配置在 localhost IP 地址 (`127.0.0.1`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e829c-1037">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="e829c-1038">如果动态端口为 1234，则 Kestrel 在 `127.0.0.1:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="e829c-1038">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="e829c-1039">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="e829c-1039">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="e829c-1040">Kestrel 的侦听 API</span><span class="sxs-lookup"><span data-stu-id="e829c-1040">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="e829c-1041">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="e829c-1041">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="e829c-1042">使用模块时，不需要调用 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="e829c-1042">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="e829c-1043">如果调用 `UseUrls` 或 `Listen`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="e829c-1043">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="e829c-1044">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-1044">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e829c-1045">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1045">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="e829c-1046">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="e829c-1046">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="e829c-1047">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="e829c-1047">Enable the IISIntegration components</span></span>

<span data-ttu-id="e829c-1048">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="e829c-1048">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="e829c-1049">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="e829c-1049">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="e829c-1050">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="e829c-1050">IIS options</span></span>

| <span data-ttu-id="e829c-1051">选项</span><span class="sxs-lookup"><span data-stu-id="e829c-1051">Option</span></span>                         | <span data-ttu-id="e829c-1052">默认</span><span class="sxs-lookup"><span data-stu-id="e829c-1052">Default</span></span> | <span data-ttu-id="e829c-1053">设置</span><span class="sxs-lookup"><span data-stu-id="e829c-1053">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e829c-1054">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="e829c-1054">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e829c-1055">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="e829c-1055">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e829c-1056">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-1056">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e829c-1057">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1057">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e829c-1058">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="e829c-1058">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="e829c-1059">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-1059">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="e829c-1060">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="e829c-1060">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="e829c-1061">选项</span><span class="sxs-lookup"><span data-stu-id="e829c-1061">Option</span></span>                         | <span data-ttu-id="e829c-1062">默认</span><span class="sxs-lookup"><span data-stu-id="e829c-1062">Default</span></span> | <span data-ttu-id="e829c-1063">设置</span><span class="sxs-lookup"><span data-stu-id="e829c-1063">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e829c-1064">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="e829c-1064">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e829c-1065">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="e829c-1065">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e829c-1066">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-1066">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e829c-1067">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="e829c-1067">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e829c-1068">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="e829c-1068">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="e829c-1069">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="e829c-1069">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e829c-1070">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="e829c-1070">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e829c-1071">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="e829c-1071">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="e829c-1072">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-1072">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="e829c-1073">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1073">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="e829c-1074">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="e829c-1074">web.config file</span></span>

<span data-ttu-id="e829c-1075">web.config  文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1075">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e829c-1076">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1076">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="e829c-1077">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="e829c-1077">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="e829c-1078">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="e829c-1078">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="e829c-1079">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)    。</span><span class="sxs-lookup"><span data-stu-id="e829c-1079">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="e829c-1080">如果项目中存在 web.config  文件，则会使用正确的 processPath  和参数  转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="e829c-1080">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="e829c-1081">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="e829c-1081">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="e829c-1082">web.config  文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="e829c-1082">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="e829c-1083">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="e829c-1083">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="e829c-1084">要防止 Web SDK 转换 web.config  文件，请使用项目文件中的 \<IsTransformWebConfigDisabled>  属性：</span><span class="sxs-lookup"><span data-stu-id="e829c-1084">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="e829c-1085">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath  和参数  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1085">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="e829c-1086">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-1086">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="e829c-1087">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="e829c-1087">web.config file location</span></span>

<span data-ttu-id="e829c-1088">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config  文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="e829c-1088">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="e829c-1089">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="e829c-1089">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="e829c-1090">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1090">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="e829c-1091">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json  、\<assembly>.xml  （XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1091">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="e829c-1092">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1092">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="e829c-1093">如果缺少 web.config  文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-1093">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="e829c-1094">部署中必须始终存在 web.config  文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config  文件。**</span><span class="sxs-lookup"><span data-stu-id="e829c-1094">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="e829c-1095">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="e829c-1095">Transform web.config</span></span>

<span data-ttu-id="e829c-1096">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1096">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="e829c-1097">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="e829c-1097">IIS configuration</span></span>

<span data-ttu-id="e829c-1098">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="e829c-1098">**Windows Server operating systems**</span></span>

<span data-ttu-id="e829c-1099">启用 Web 服务器 (IIS)  服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="e829c-1099">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="e829c-1100">通过“管理”  菜单或“服务器管理器”  中的链接使用“添加角色和功能”  向导。</span><span class="sxs-lookup"><span data-stu-id="e829c-1100">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="e829c-1101">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框   。</span><span class="sxs-lookup"><span data-stu-id="e829c-1101">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="e829c-1103">在“功能”  步骤后，为 Web 服务器 (IIS) 加载“角色服务”  步骤。</span><span class="sxs-lookup"><span data-stu-id="e829c-1103">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="e829c-1104">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="e829c-1104">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="e829c-1106">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-1106">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e829c-1107">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1107">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="e829c-1108">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-1108">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e829c-1109">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1109">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e829c-1110">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-1110">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e829c-1111">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-1111">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e829c-1112">若要启用 WebSocket，请依次展开以下节点：“Web 服务器”   > “应用开发”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1112">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="e829c-1113">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-1113">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e829c-1114">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1114">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e829c-1115">继续执行“确认”步骤，安装 Web 服务器角色和服务  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1115">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="e829c-1116">安装 Web 服务器 (IIS)  角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="e829c-1116">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="e829c-1117">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="e829c-1117">**Windows desktop operating systems**</span></span>

<span data-ttu-id="e829c-1118">启用“IIS 管理控制台”  和“万维网服务”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1118">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e829c-1119">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="e829c-1119">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="e829c-1120">打开“Internet Information Services”  节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-1120">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="e829c-1121">打开“Web 管理工具”  节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-1121">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="e829c-1122">选中“IIS 管理控制台”框  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1122">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="e829c-1123">选中“万维网服务”  框。</span><span class="sxs-lookup"><span data-stu-id="e829c-1123">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="e829c-1124">接受“万维网服务”的默认功能，或自定义 IIS 功能  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1124">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="e829c-1125">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-1125">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="e829c-1126">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1126">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="e829c-1127">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-1127">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="e829c-1128">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1128">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="e829c-1129">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-1129">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="e829c-1130">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-1130">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="e829c-1131">若要启用 WebSocket，请依次展开以下节点：“万维网服务”   > “应用开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1131">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="e829c-1132">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="e829c-1132">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e829c-1133">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1133">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="e829c-1134">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="e829c-1134">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e829c-1136">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="e829c-1136">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e829c-1137">在托管系统上安装 .NET Core 托管捆绑包  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1137">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="e829c-1138">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1138">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e829c-1139">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="e829c-1139">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e829c-1140">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="e829c-1140">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="e829c-1141">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-1141">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="e829c-1142">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="e829c-1142">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="e829c-1143">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="e829c-1143">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="e829c-1144">下载</span><span class="sxs-lookup"><span data-stu-id="e829c-1144">Download</span></span>

1. <span data-ttu-id="e829c-1145">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="e829c-1145">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="e829c-1146">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-1146">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="e829c-1147">在“运行应用 - 运行时”  列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="e829c-1147">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="e829c-1148">使用“托管捆绑包”链接下载安装程序  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1148">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="e829c-1149">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="e829c-1149">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="e829c-1150">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1150">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="e829c-1151">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="e829c-1151">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="e829c-1152">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-1152">Run the installer on the server.</span></span> <span data-ttu-id="e829c-1153">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="e829c-1153">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="e829c-1154">`OPT_NO_ANCM=1` &ndash; 跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="e829c-1154">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="e829c-1155">`OPT_NO_RUNTIME=1` &ndash; 跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-1155">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="e829c-1156">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="e829c-1156">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e829c-1157">`OPT_NO_SHAREDFX=1` &ndash; 跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="e829c-1157">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="e829c-1158">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="e829c-1158">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e829c-1159">`OPT_NO_X86=1` &ndash; 跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-1159">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="e829c-1160">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="e829c-1160">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="e829c-1161">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-1161">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="e829c-1162">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 当共享配置 (applicationHost.config  ) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="e829c-1162">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="e829c-1163">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。 </span><span class="sxs-lookup"><span data-stu-id="e829c-1163">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="e829c-1164">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="e829c-1164">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="e829c-1165">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="e829c-1165">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="e829c-1166">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="e829c-1166">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="e829c-1167">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="e829c-1167">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="e829c-1168">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="e829c-1168">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="e829c-1169">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="e829c-1169">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="e829c-1170">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="e829c-1170">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="e829c-1171">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="e829c-1171">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="e829c-1172">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="e829c-1172">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="e829c-1173">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1173">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="e829c-1174">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="e829c-1174">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="e829c-1175">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="e829c-1175">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="e829c-1176">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-1176">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="e829c-1177">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="e829c-1177">The preferred method is to use WebPI.</span></span> <span data-ttu-id="e829c-1178">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="e829c-1178">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="e829c-1179">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="e829c-1179">Create the IIS site</span></span>

1. <span data-ttu-id="e829c-1180">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-1180">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="e829c-1181">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="e829c-1181">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="e829c-1182">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="e829c-1182">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="e829c-1183">在 IIS 管理器中，打开“连接”  面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="e829c-1183">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="e829c-1184">右键单击“站点”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="e829c-1184">Right-click the **Sites** folder.</span></span> <span data-ttu-id="e829c-1185">选择上下文菜单中的“添加网站”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1185">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="e829c-1186">提供网站名称，并将物理路径设置为应用的部署文件夹   。</span><span class="sxs-lookup"><span data-stu-id="e829c-1186">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="e829c-1187">提供“绑定”  配置，并通过选择“确定”  创建网站：</span><span class="sxs-lookup"><span data-stu-id="e829c-1187">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="e829c-1189">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1189">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e829c-1190">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="e829c-1190">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="e829c-1191">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="e829c-1191">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e829c-1192">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="e829c-1192">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="e829c-1193">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="e829c-1193">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e829c-1194">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1194">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="e829c-1195">在服务器节点下，选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1195">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="e829c-1196">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1196">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="e829c-1197">在“编辑应用程序池”  窗口中，将“.NET CLR 版本”  设置为“无托管代码”  ：</span><span class="sxs-lookup"><span data-stu-id="e829c-1197">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="e829c-1199">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="e829c-1199">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="e829c-1200">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-1200">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="e829c-1201">将“.NET CLR 版本”  设置为“无托管代码”  是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="e829c-1201">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="e829c-1202">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-1202">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="e829c-1203">在 IIS 管理器 >“应用程序池”  的“操作”  侧栏中，选择“设置应用程序池默认设置”  或“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1203">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="e829c-1204">找到“启用 32 位应用程序”并将值设置为 `False` 。</span><span class="sxs-lookup"><span data-stu-id="e829c-1204">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="e829c-1205">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-1205">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="e829c-1206">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-1206">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="e829c-1207">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限    。</span><span class="sxs-lookup"><span data-stu-id="e829c-1207">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="e829c-1208">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-1208">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="e829c-1209">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="e829c-1209">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="e829c-1210">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1210">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="e829c-1211">部署应用</span><span class="sxs-lookup"><span data-stu-id="e829c-1211">Deploy the app</span></span>

<span data-ttu-id="e829c-1212">将应用程序部署到 IIS 物理路径  文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="e829c-1212">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="e829c-1213">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布  文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="e829c-1213">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="e829c-1214">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="e829c-1214">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="e829c-1215">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1215">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="e829c-1216">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”  对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="e829c-1216">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="e829c-1218">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="e829c-1218">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="e829c-1219">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1219">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="e829c-1220">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="e829c-1220">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="e829c-1221">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="e829c-1221">Alternatives to Web Deploy</span></span>

<span data-ttu-id="e829c-1222">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="e829c-1222">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="e829c-1223">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="e829c-1223">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="e829c-1224">浏览网站</span><span class="sxs-lookup"><span data-stu-id="e829c-1224">Browse the website</span></span>

<span data-ttu-id="e829c-1225">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-1225">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="e829c-1226">在以下示例中，站点被绑定到端口  `80` 上 `www.mysite.com` 的 IIS 主机名  中。</span><span class="sxs-lookup"><span data-stu-id="e829c-1226">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="e829c-1227">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="e829c-1227">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="e829c-1229">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="e829c-1229">Locked deployment files</span></span>

<span data-ttu-id="e829c-1230">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="e829c-1230">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="e829c-1231">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-1231">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="e829c-1232">若要在部署中解除已锁定的文件，请使用以下方法之一  停止应用池：</span><span class="sxs-lookup"><span data-stu-id="e829c-1232">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="e829c-1233">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="e829c-1233">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="e829c-1234">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1234">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="e829c-1235">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1235">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="e829c-1236">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1236">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="e829c-1237">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-1237">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="e829c-1238">使用 PowerShell 删除 app_offline.html  （需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="e829c-1238">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="e829c-1239">数据保护</span><span class="sxs-lookup"><span data-stu-id="e829c-1239">Data protection</span></span>

<span data-ttu-id="e829c-1240">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="e829c-1240">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="e829c-1241">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1241">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="e829c-1242">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="e829c-1242">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="e829c-1243">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="e829c-1243">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="e829c-1244">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="e829c-1244">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="e829c-1245">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="e829c-1245">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="e829c-1246">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="e829c-1246">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="e829c-1247">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1247">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="e829c-1248">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="e829c-1248">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="e829c-1249">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="e829c-1249">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="e829c-1250">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="e829c-1250">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="e829c-1251">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="e829c-1251">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="e829c-1252">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1252">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="e829c-1253">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="e829c-1253">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="e829c-1254">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="e829c-1254">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="e829c-1255">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="e829c-1255">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="e829c-1256">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="e829c-1256">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="e829c-1257">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="e829c-1257">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="e829c-1258">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="e829c-1258">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="e829c-1259">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="e829c-1259">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="e829c-1260">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1260">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="e829c-1261">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="e829c-1261">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="e829c-1262">此设置位于应用池“高级设置”下的“进程模型”部分   。</span><span class="sxs-lookup"><span data-stu-id="e829c-1262">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="e829c-1263">将“加载用户配置文件”设置为 `True` 。</span><span class="sxs-lookup"><span data-stu-id="e829c-1263">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="e829c-1264">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="e829c-1264">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="e829c-1265">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="e829c-1265">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="e829c-1266">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1266">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="e829c-1267">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="e829c-1267">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="e829c-1268">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="e829c-1268">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="e829c-1269">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e829c-1269">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="e829c-1270">导航到 %windir%/system32/inetsrv/config  文件夹。</span><span class="sxs-lookup"><span data-stu-id="e829c-1270">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="e829c-1271">打开 applicationHost.config  文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-1271">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="e829c-1272">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="e829c-1272">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="e829c-1273">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="e829c-1273">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="e829c-1274">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="e829c-1274">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="e829c-1275">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1275">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="e829c-1276">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="e829c-1276">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="e829c-1277">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="e829c-1277">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="e829c-1278">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="e829c-1278">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="e829c-1279">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="e829c-1279">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="e829c-1280">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="e829c-1280">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="e829c-1281">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1281">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="e829c-1282">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="e829c-1282">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="e829c-1283">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1283">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="e829c-1284">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="e829c-1284">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="e829c-1285">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="e829c-1285">Virtual Directories</span></span>

<span data-ttu-id="e829c-1286">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1286">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="e829c-1287">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1287">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="e829c-1288">子应用程序</span><span class="sxs-lookup"><span data-stu-id="e829c-1288">Sub-applications</span></span>

<span data-ttu-id="e829c-1289">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1289">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="e829c-1290">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="e829c-1290">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="e829c-1291">子应用不应将 ASP.NET Core 模块作为处理程序包含在其中。</span><span class="sxs-lookup"><span data-stu-id="e829c-1291">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="e829c-1292">如果在子应用的 web.config  文件中将该模块添加为处理程序，则在尝试浏览子应用时会收到“500.19 内部服务器错误”  ，即引用错误的配置文件。</span><span class="sxs-lookup"><span data-stu-id="e829c-1292">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="e829c-1293">以下示例显示 ASP.NET Core 子应用的已发布 web.config  文件：</span><span class="sxs-lookup"><span data-stu-id="e829c-1293">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="e829c-1294">在 ASP.NET Core 应用下托管非 ASP.NET Core 子应用时，需在子应用的 web.config 文件中显式删除继承的处理程序  ：</span><span class="sxs-lookup"><span data-stu-id="e829c-1294">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

<span data-ttu-id="e829c-1295">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="e829c-1295">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="e829c-1296">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="e829c-1296">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="e829c-1297">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="e829c-1297">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="e829c-1298">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="e829c-1298">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="e829c-1299">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="e829c-1299">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="e829c-1300">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="e829c-1300">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="e829c-1301">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”  响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="e829c-1301">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="e829c-1302">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="e829c-1302">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="e829c-1303">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-1303">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="e829c-1304">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中   。</span><span class="sxs-lookup"><span data-stu-id="e829c-1304">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="e829c-1305">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="e829c-1305">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="e829c-1306">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。 </span><span class="sxs-lookup"><span data-stu-id="e829c-1306">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="e829c-1307">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。   </span><span class="sxs-lookup"><span data-stu-id="e829c-1307">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="e829c-1308">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1308">Select **OK**.</span></span>

<span data-ttu-id="e829c-1309">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-1309">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="e829c-1310">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e829c-1310">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="e829c-1311">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="e829c-1311">Configuration of IIS with web.config</span></span>

<span data-ttu-id="e829c-1312">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config  的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="e829c-1312">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="e829c-1313">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="e829c-1313">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="e829c-1314">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config  文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="e829c-1314">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="e829c-1315">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="e829c-1315">For more information, see the following topics:</span></span>

* [<span data-ttu-id="e829c-1316">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="e829c-1316">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="e829c-1317">若要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”  部分。</span><span class="sxs-lookup"><span data-stu-id="e829c-1317">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="e829c-1318">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="e829c-1318">Configuration sections of web.config</span></span>

<span data-ttu-id="e829c-1319">ASP.NET Core 应用不会使用 web.config  中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="e829c-1319">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="e829c-1320">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="e829c-1320">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="e829c-1321">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1321">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="e829c-1322">应用程序池</span><span class="sxs-lookup"><span data-stu-id="e829c-1322">Application Pools</span></span>

<span data-ttu-id="e829c-1323">在服务器上托管多个网站时，建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="e829c-1323">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="e829c-1324">IIS“添加网站”对话框默认执行此配置  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1324">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="e829c-1325">提供了站点名称时，该文本会自动传输到“应用程序池”文本框   。</span><span class="sxs-lookup"><span data-stu-id="e829c-1325">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="e829c-1326">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="e829c-1326">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="e829c-1327">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="e829c-1327">Application Pool Identity</span></span>

<span data-ttu-id="e829c-1328">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="e829c-1328">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="e829c-1329">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="e829c-1329">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="e829c-1330">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity    ：</span><span class="sxs-lookup"><span data-stu-id="e829c-1330">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="e829c-1332">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="e829c-1332">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="e829c-1333">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="e829c-1333">Resources can be secured using this identity.</span></span> <span data-ttu-id="e829c-1334">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="e829c-1334">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="e829c-1335">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="e829c-1335">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="e829c-1336">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="e829c-1336">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="e829c-1337">右键单击该目录，然后选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1337">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="e829c-1338">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮    。</span><span class="sxs-lookup"><span data-stu-id="e829c-1338">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="e829c-1339">选择“位置”按钮，并确保该系统处于选中状态  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1339">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="e829c-1340">在“输入要选择的对象名称”  区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="e829c-1340">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="e829c-1341">选择“检查名称”按钮  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1341">Select the **Check Names** button.</span></span> <span data-ttu-id="e829c-1342">有关 DefaultAppPool  ，请检查使用 IIS AppPool\DefaultAppPool  的名称。</span><span class="sxs-lookup"><span data-stu-id="e829c-1342">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="e829c-1343">当选择“检查名称”  按钮时，对象名称区域中会显示 DefaultAppPool  的值。</span><span class="sxs-lookup"><span data-stu-id="e829c-1343">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="e829c-1344">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="e829c-1344">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="e829c-1345">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="e829c-1345">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="e829c-1347">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="e829c-1347">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="e829c-1349">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-1349">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="e829c-1350">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-1350">Provide additional permissions as needed.</span></span>

<span data-ttu-id="e829c-1351">也可使用 ICACLS  工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="e829c-1351">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="e829c-1352">以 DefaultAppPool  为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="e829c-1352">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="e829c-1353">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="e829c-1353">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e829c-1354">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="e829c-1354">HTTP/2 support</span></span>

<span data-ttu-id="e829c-1355">满足以下基本要求的进程外部署支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="e829c-1355">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="e829c-1356">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e829c-1356">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="e829c-1357">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e829c-1357">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="e829c-1358">目标框架：不适用于进程外部署，因为 HTTP/2 连接完全由 IIS 处理。</span><span class="sxs-lookup"><span data-stu-id="e829c-1358">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="e829c-1359">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="e829c-1359">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e829c-1360">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="e829c-1360">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="e829c-1361">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="e829c-1361">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e829c-1362">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e829c-1362">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="e829c-1363">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1363">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="e829c-1364">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="e829c-1364">CORS preflight requests</span></span>

<span data-ttu-id="e829c-1365">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。 </span><span class="sxs-lookup"><span data-stu-id="e829c-1365">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="e829c-1366">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="e829c-1366">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="e829c-1367">若要了解如何在 web.config  中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="e829c-1367">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="e829c-1368">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="e829c-1368">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="e829c-1369">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="e829c-1369">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="e829c-1370">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="e829c-1370">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="e829c-1371">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="e829c-1371">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="e829c-1372">其他资源</span><span class="sxs-lookup"><span data-stu-id="e829c-1372">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="e829c-1373">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="e829c-1373">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="e829c-1374">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="e829c-1374">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="e829c-1375">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e829c-1375">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
