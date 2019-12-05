---
title: 使用 IIS 在 Windows 上托管 ASP.NET Core
author: guardrex
description: 了解如何在 Windows Server Internet Information Services (IIS) 上托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/26/2019
uid: host-and-deploy/iis/index
ms.openlocfilehash: de1b3e270ccd90bde741975de38a224e557f1a08
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717411"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="4e959-103">使用 IIS 在 Windows 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e959-103">Host ASP.NET Core on Windows with IIS</span></span>

<span data-ttu-id="4e959-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4e959-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4e959-105">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="4e959-105">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="4e959-106">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="4e959-106">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="4e959-107">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="4e959-107">Supported operating systems</span></span>

<span data-ttu-id="4e959-108">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="4e959-108">The following operating systems are supported:</span></span>

* <span data-ttu-id="4e959-109">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4e959-109">Windows 7 or later</span></span>
* <span data-ttu-id="4e959-110">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4e959-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4e959-111">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="4e959-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="4e959-112">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="4e959-112">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="4e959-113">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="4e959-113">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="4e959-114">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="4e959-114">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="4e959-115">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="4e959-115">Supported platforms</span></span>

<span data-ttu-id="4e959-116">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="4e959-116">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="4e959-117">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="4e959-117">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="4e959-118">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="4e959-118">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="4e959-119">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="4e959-119">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="4e959-120">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="4e959-120">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="4e959-121">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="4e959-121">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="4e959-122">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="4e959-122">A 64-bit runtime must be present on the host system.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="hosting-models"></a><span data-ttu-id="4e959-123">托管模型</span><span class="sxs-lookup"><span data-stu-id="4e959-123">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="4e959-124">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="4e959-124">In-process hosting model</span></span>

<span data-ttu-id="4e959-125">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="4e959-125">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="4e959-126">进程内承载相较进程外承载提供更优的性能，因为请求并不通过环回适配器进行代理，环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="4e959-126">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="4e959-127">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="4e959-127">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4e959-128">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="4e959-128">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="4e959-129">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="4e959-129">Performs app initialization.</span></span>
  * <span data-ttu-id="4e959-130">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="4e959-130">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="4e959-131">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="4e959-131">Calls `Program.Main`.</span></span>
* <span data-ttu-id="4e959-132">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="4e959-132">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="4e959-133">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="4e959-133">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="4e959-134">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="4e959-134">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

<span data-ttu-id="4e959-136">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="4e959-136">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="4e959-137">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="4e959-137">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="4e959-138">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="4e959-138">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="4e959-139">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="4e959-139">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="4e959-140">IIS HTTP 服务器处理请求之后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="4e959-140">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="4e959-141">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="4e959-141">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="4e959-142">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="4e959-142">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="4e959-143">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="4e959-143">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="4e959-144">进程内托管选择使用现有应用，但 [dotnet new](/dotnet/core/tools/dotnet-new) 模板默认使用所有 IIS 和 IIS Express 方案的进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="4e959-144">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="4e959-145">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。  </span><span class="sxs-lookup"><span data-stu-id="4e959-145">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="4e959-146">性能测试表明，与在进程外托管应用并将请求代理到 [Kestrel](xref:fundamentals/servers/kestrel) 服务器相比，在进程中托管 .NET Core 应用可以大大提升请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="4e959-146">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

> [!NOTE]
> <span data-ttu-id="4e959-147">作为单个文件可执行文件发布的应用无法由进程内托管模型加载。</span><span class="sxs-lookup"><span data-stu-id="4e959-147">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="4e959-148">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="4e959-148">Out-of-process hosting model</span></span>

<span data-ttu-id="4e959-149">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="4e959-149">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="4e959-150">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="4e959-150">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="4e959-151">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="4e959-151">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4e959-152">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="4e959-152">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="4e959-154">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="4e959-154">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="4e959-155">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="4e959-155">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="4e959-156">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="4e959-156">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="4e959-157">该模块在启动时通过环境变量指定端口，<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="4e959-157">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="4e959-158">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="4e959-158">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="4e959-159">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="4e959-159">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="4e959-160">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="4e959-160">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="4e959-161">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="4e959-161">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="4e959-162">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="4e959-162">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="4e959-163">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="4e959-163">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4e959-164">ASP.NET Core 随附有 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，后者是默认的跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="4e959-164">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="4e959-165">使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用在独立于 IIS 工作进程（进程外）  和 [Kestrel 服务器](xref:fundamentals/servers/index#kestrel)的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="4e959-165">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="4e959-166">由于 ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，因此该模块会处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="4e959-166">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="4e959-167">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="4e959-167">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="4e959-168">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="4e959-168">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4e959-169">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="4e959-169">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="4e959-171">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="4e959-171">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="4e959-172">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="4e959-172">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="4e959-173">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="4e959-173">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="4e959-174">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="4e959-174">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="4e959-175">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="4e959-175">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="4e959-176">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="4e959-176">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="4e959-177">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="4e959-177">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="4e959-178">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="4e959-178">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="4e959-179">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="4e959-179">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="4e959-180">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="4e959-180">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="4e959-181">`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的基础路径和端口来启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="4e959-181">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="4e959-182">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="4e959-182">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="4e959-183">`CreateDefaultBuilder` 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 方法。</span><span class="sxs-lookup"><span data-stu-id="4e959-183">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="4e959-184">`UseIISIntegration` 配置在 localhost IP 地址 (`127.0.0.1`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="4e959-184">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="4e959-185">如果动态端口为 1234，则 Kestrel 在 `127.0.0.1:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="4e959-185">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="4e959-186">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="4e959-186">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="4e959-187">Kestrel 的侦听 API</span><span class="sxs-lookup"><span data-stu-id="4e959-187">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="4e959-188">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="4e959-188">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="4e959-189">使用模块时，不需要调用 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="4e959-189">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="4e959-190">如果调用 `UseUrls` 或 `Listen`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="4e959-190">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

::: moniker-end

<span data-ttu-id="4e959-191">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="4e959-191">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="4e959-192">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="4e959-192">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="4e959-193">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="4e959-193">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="4e959-194">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="4e959-194">Enable the IISIntegration components</span></span>

<span data-ttu-id="4e959-195">典型 Program.cs 会调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，以开始设置将实现与 IIS 的集成的主机  ：</span><span class="sxs-lookup"><span data-stu-id="4e959-195">A typical *Program.cs* calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to begin setting up a host that enables integration with IIS:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

### <a name="iis-options"></a><span data-ttu-id="4e959-196">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="4e959-196">IIS options</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4e959-197">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="4e959-197">**In-process hosting model**</span></span>

<span data-ttu-id="4e959-198">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="4e959-198">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="4e959-199">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="4e959-199">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="4e959-200">选项</span><span class="sxs-lookup"><span data-stu-id="4e959-200">Option</span></span>                         | <span data-ttu-id="4e959-201">默认</span><span class="sxs-lookup"><span data-stu-id="4e959-201">Default</span></span> | <span data-ttu-id="4e959-202">设置</span><span class="sxs-lookup"><span data-stu-id="4e959-202">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="4e959-203">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="4e959-203">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e959-204">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="4e959-204">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="4e959-205">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="4e959-205">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="4e959-206">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="4e959-206">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="4e959-207">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="4e959-207">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="4e959-208">`HttpContext.Request` 和 `HttpContext.Response` 是否允许同步 IO。</span><span class="sxs-lookup"><span data-stu-id="4e959-208">Whether synchronous IO is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="4e959-209">获取或设置 `HttpRequest` 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="4e959-209">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="4e959-210">请注意，IIS 本身有限制 `maxAllowedContentLength`，这一限制将在 `IISServerOptions` 中设置 `MaxRequestBodySize` 之前进行处理。</span><span class="sxs-lookup"><span data-stu-id="4e959-210">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="4e959-211">更改 `MaxRequestBodySize` 不会影响 `maxAllowedContentLength`。</span><span class="sxs-lookup"><span data-stu-id="4e959-211">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="4e959-212">若要增加 `maxAllowedContentLength`，请在 web.config  中添加一个条目，将 `maxAllowedContentLength` 设置为更高值。</span><span class="sxs-lookup"><span data-stu-id="4e959-212">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="4e959-213">有关更多详细信息，请参阅[配置](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。</span><span class="sxs-lookup"><span data-stu-id="4e959-213">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="4e959-214">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="4e959-214">**Out-of-process hosting model**</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="4e959-215">选项</span><span class="sxs-lookup"><span data-stu-id="4e959-215">Option</span></span>                         | <span data-ttu-id="4e959-216">默认</span><span class="sxs-lookup"><span data-stu-id="4e959-216">Default</span></span> | <span data-ttu-id="4e959-217">设置</span><span class="sxs-lookup"><span data-stu-id="4e959-217">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="4e959-218">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="4e959-218">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e959-219">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="4e959-219">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="4e959-220">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="4e959-220">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="4e959-221">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="4e959-221">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="4e959-222">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="4e959-222">Sets the display name shown to users on login pages.</span></span> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4e959-223">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="4e959-223">**Out-of-process hosting model**</span></span>

::: moniker-end

<span data-ttu-id="4e959-224">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="4e959-224">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="4e959-225">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="4e959-225">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="4e959-226">选项</span><span class="sxs-lookup"><span data-stu-id="4e959-226">Option</span></span>                         | <span data-ttu-id="4e959-227">默认</span><span class="sxs-lookup"><span data-stu-id="4e959-227">Default</span></span> | <span data-ttu-id="4e959-228">设置</span><span class="sxs-lookup"><span data-stu-id="4e959-228">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="4e959-229">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="4e959-229">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="4e959-230">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="4e959-230">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="4e959-231">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="4e959-231">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="4e959-232">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="4e959-232">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="4e959-233">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="4e959-233">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="4e959-234">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="4e959-234">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4e959-235">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="4e959-235">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4e959-236">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4e959-236">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="4e959-237">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="4e959-237">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="4e959-238">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="4e959-238">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="4e959-239">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="4e959-239">web.config file</span></span>

<span data-ttu-id="4e959-240">web.config  文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="4e959-240">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="4e959-241">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="4e959-241">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="4e959-242">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="4e959-242">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="4e959-243">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="4e959-243">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="4e959-244">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)    。</span><span class="sxs-lookup"><span data-stu-id="4e959-244">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="4e959-245">如果项目中存在 web.config  文件，则会使用正确的 processPath  和参数  转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="4e959-245">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="4e959-246">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="4e959-246">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="4e959-247">web.config  文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="4e959-247">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="4e959-248">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="4e959-248">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="4e959-249">要防止 Web SDK 转换 web.config  文件，请使用项目文件中的 \<IsTransformWebConfigDisabled>  属性：</span><span class="sxs-lookup"><span data-stu-id="4e959-249">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="4e959-250">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath  和参数  。</span><span class="sxs-lookup"><span data-stu-id="4e959-250">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="4e959-251">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="4e959-251">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="4e959-252">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="4e959-252">web.config file location</span></span>

<span data-ttu-id="4e959-253">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config  文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="4e959-253">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="4e959-254">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="4e959-254">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="4e959-255">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="4e959-255">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="4e959-256">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json  、\<assembly>.xml  （XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="4e959-256">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="4e959-257">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供  。</span><span class="sxs-lookup"><span data-stu-id="4e959-257">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="4e959-258">如果缺少 web.config  文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="4e959-258">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="4e959-259">部署中必须始终存在 web.config  文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config  文件。**</span><span class="sxs-lookup"><span data-stu-id="4e959-259">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="4e959-260">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="4e959-260">Transform web.config</span></span>

<span data-ttu-id="4e959-261">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="4e959-261">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="4e959-262">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="4e959-262">IIS configuration</span></span>

<span data-ttu-id="4e959-263">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="4e959-263">**Windows Server operating systems**</span></span>

<span data-ttu-id="4e959-264">启用 Web 服务器 (IIS)  服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="4e959-264">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="4e959-265">通过“管理”  菜单或“服务器管理器”  中的链接使用“添加角色和功能”  向导。</span><span class="sxs-lookup"><span data-stu-id="4e959-265">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="4e959-266">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框   。</span><span class="sxs-lookup"><span data-stu-id="4e959-266">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="4e959-268">在“功能”  步骤后，为 Web 服务器 (IIS) 加载“角色服务”  步骤。</span><span class="sxs-lookup"><span data-stu-id="4e959-268">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="4e959-269">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="4e959-269">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="4e959-271">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="4e959-271">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="4e959-272">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-272">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="4e959-273">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="4e959-273">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="4e959-274">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="4e959-274">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="4e959-275">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="4e959-275">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="4e959-276">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="4e959-276">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="4e959-277">若要启用 WebSocket，请依次展开以下节点：“Web 服务器”   > “应用开发”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-277">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="4e959-278">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="4e959-278">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="4e959-279">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="4e959-279">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="4e959-280">继续执行“确认”步骤，安装 Web 服务器角色和服务  。</span><span class="sxs-lookup"><span data-stu-id="4e959-280">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="4e959-281">安装 Web 服务器 (IIS)  角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="4e959-281">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="4e959-282">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="4e959-282">**Windows desktop operating systems**</span></span>

<span data-ttu-id="4e959-283">启用“IIS 管理控制台”  和“万维网服务”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-283">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="4e959-284">导航到“控制面板” > “程序” > “程序和功能” > “打开或关闭 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="4e959-284">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="4e959-285">打开“Internet Information Services”  节点。</span><span class="sxs-lookup"><span data-stu-id="4e959-285">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="4e959-286">打开“Web 管理工具”  节点。</span><span class="sxs-lookup"><span data-stu-id="4e959-286">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="4e959-287">选中“IIS 管理控制台”框  。</span><span class="sxs-lookup"><span data-stu-id="4e959-287">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="4e959-288">选中“万维网服务”  框。</span><span class="sxs-lookup"><span data-stu-id="4e959-288">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="4e959-289">接受“万维网服务”的默认功能，或自定义 IIS 功能  。</span><span class="sxs-lookup"><span data-stu-id="4e959-289">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="4e959-290">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="4e959-290">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="4e959-291">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务”   > “安全”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-291">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="4e959-292">选择“Windows 身份验证”  功能。</span><span class="sxs-lookup"><span data-stu-id="4e959-292">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="4e959-293">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="4e959-293">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="4e959-294">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="4e959-294">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="4e959-295">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="4e959-295">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="4e959-296">若要启用 WebSocket，请依次展开以下节点：“万维网服务”   > “应用开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-296">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="4e959-297">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="4e959-297">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="4e959-298">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="4e959-298">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="4e959-299">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="4e959-299">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="4e959-301">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="4e959-301">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="4e959-302">在托管系统上安装 .NET Core 托管捆绑包  。</span><span class="sxs-lookup"><span data-stu-id="4e959-302">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="4e959-303">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="4e959-303">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="4e959-304">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="4e959-304">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4e959-305">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="4e959-305">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="4e959-306">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="4e959-306">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="4e959-307">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="4e959-307">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="4e959-308">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="4e959-308">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="4e959-309">直接下载（当前版本）</span><span class="sxs-lookup"><span data-stu-id="4e959-309">Direct download (current version)</span></span>

<span data-ttu-id="4e959-310">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="4e959-310">Download the installer using the following link:</span></span>

[<span data-ttu-id="4e959-311">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="4e959-311">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="4e959-312">先前版本的安装程序</span><span class="sxs-lookup"><span data-stu-id="4e959-312">Earlier versions of the installer</span></span>

<span data-ttu-id="4e959-313">若要获取先前版本的安装程序：</span><span class="sxs-lookup"><span data-stu-id="4e959-313">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="4e959-314">导航到 [.NET 下载存档](https://www.microsoft.com/net/download/archives)。</span><span class="sxs-lookup"><span data-stu-id="4e959-314">Navigate to the [.NET download archives](https://www.microsoft.com/net/download/archives).</span></span>
1. <span data-ttu-id="4e959-315">在“.NET Core”  下，选择 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="4e959-315">Under **.NET Core**, select the .NET Core version.</span></span>
1. <span data-ttu-id="4e959-316">在“运行应用 - 运行时”  列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="4e959-316">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="4e959-317">使用“运行时和托管捆绑包”  链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="4e959-317">Download the installer using the **Runtime & Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="4e959-318">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="4e959-318">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="4e959-319">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="4e959-319">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="4e959-320">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="4e959-320">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="4e959-321">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="4e959-321">Run the installer on the server.</span></span> <span data-ttu-id="4e959-322">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="4e959-322">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="4e959-323">`OPT_NO_ANCM=1` &ndash; 跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="4e959-323">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="4e959-324">`OPT_NO_RUNTIME=1` &ndash; 跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="4e959-324">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="4e959-325">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="4e959-325">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="4e959-326">`OPT_NO_SHAREDFX=1` &ndash; 跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="4e959-326">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="4e959-327">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="4e959-327">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="4e959-328">`OPT_NO_X86=1` &ndash; 跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="4e959-328">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="4e959-329">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="4e959-329">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="4e959-330">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="4e959-330">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="4e959-331">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 当共享配置 (applicationHost.config  ) 与 IIS 安装位于同一台计算机上时，禁用检查使用的是否是 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="4e959-331">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="4e959-332">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。 </span><span class="sxs-lookup"><span data-stu-id="4e959-332">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="4e959-333">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="4e959-333">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="4e959-334">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4e959-334">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="4e959-335">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="4e959-335">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4e959-336">ASP.NET Core 不采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="4e959-336">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="4e959-337">通过安装新的托管捆绑包升级共享框架后，请重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4e959-337">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e959-338">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="4e959-338">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="4e959-339">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="4e959-339">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="4e959-340">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="4e959-340">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="4e959-341">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="4e959-341">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="4e959-342">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="4e959-342">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="4e959-343">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="4e959-343">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="4e959-344">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="4e959-344">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="4e959-345">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="4e959-345">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="4e959-346">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="4e959-346">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="4e959-347">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="4e959-347">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="4e959-348">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="4e959-348">The preferred method is to use WebPI.</span></span> <span data-ttu-id="4e959-349">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="4e959-349">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="4e959-350">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="4e959-350">Create the IIS site</span></span>

1. <span data-ttu-id="4e959-351">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="4e959-351">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="4e959-352">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="4e959-352">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="4e959-353">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="4e959-353">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="4e959-354">在 IIS 管理器中，打开“连接”  面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="4e959-354">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="4e959-355">右键单击“站点”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4e959-355">Right-click the **Sites** folder.</span></span> <span data-ttu-id="4e959-356">选择上下文菜单中的“添加网站”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-356">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="4e959-357">提供网站名称，并将物理路径设置为应用的部署文件夹   。</span><span class="sxs-lookup"><span data-stu-id="4e959-357">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="4e959-358">提供“绑定”  配置，并通过选择“确定”  创建网站：</span><span class="sxs-lookup"><span data-stu-id="4e959-358">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="4e959-360">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="4e959-360">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="4e959-361">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="4e959-361">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="4e959-362">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="4e959-362">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="4e959-363">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="4e959-363">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="4e959-364">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="4e959-364">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="4e959-365">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="4e959-365">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="4e959-366">在服务器节点下，选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-366">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="4e959-367">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-367">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="4e959-368">在“编辑应用程序池”  窗口中，将“.NET CLR 版本”  设置为“无托管代码”  ：</span><span class="sxs-lookup"><span data-stu-id="4e959-368">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="4e959-370">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="4e959-370">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="4e959-371">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="4e959-371">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="4e959-372">将“.NET CLR 版本”  设置为“无托管代码”  是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="4e959-372">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="4e959-373">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="4e959-373">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="4e959-374">在 IIS 管理器 >“应用程序池”  的“操作”  侧栏中，选择“设置应用程序池默认设置”  或“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-374">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="4e959-375">找到“启用 32 位应用程序”并将值设置为 `False`  。</span><span class="sxs-lookup"><span data-stu-id="4e959-375">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="4e959-376">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="4e959-376">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="4e959-377">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="4e959-377">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="4e959-378">如果将应用池的默认标识（“进程模型” > “标识”）从 ApplicationPoolIdentity 更改为另一标识，请验证新标识拥有所需的权限，可访问应用的文件夹、数据库和其他所需资源    。</span><span class="sxs-lookup"><span data-stu-id="4e959-378">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="4e959-379">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="4e959-379">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="4e959-380">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="4e959-380">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="4e959-381">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="4e959-381">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="4e959-382">部署应用</span><span class="sxs-lookup"><span data-stu-id="4e959-382">Deploy the app</span></span>

<span data-ttu-id="4e959-383">将应用程序部署到 IIS 物理路径  文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="4e959-383">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="4e959-384">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布  文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="4e959-384">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="4e959-385">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="4e959-385">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="4e959-386">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="4e959-386">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="4e959-387">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”  对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="4e959-387">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="4e959-389">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="4e959-389">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="4e959-390">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="4e959-390">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="4e959-391">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="4e959-391">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="4e959-392">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="4e959-392">Alternatives to Web Deploy</span></span>

<span data-ttu-id="4e959-393">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="4e959-393">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="4e959-394">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="4e959-394">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="4e959-395">浏览网站</span><span class="sxs-lookup"><span data-stu-id="4e959-395">Browse the website</span></span>

<span data-ttu-id="4e959-396">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="4e959-396">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="4e959-397">在以下示例中，站点被绑定到端口  `80` 上 `www.mysite.com` 的 IIS 主机名  中。</span><span class="sxs-lookup"><span data-stu-id="4e959-397">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="4e959-398">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="4e959-398">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="4e959-400">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="4e959-400">Locked deployment files</span></span>

<span data-ttu-id="4e959-401">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="4e959-401">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="4e959-402">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="4e959-402">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="4e959-403">若要在部署中解除已锁定的文件，请使用以下方法之一  停止应用池：</span><span class="sxs-lookup"><span data-stu-id="4e959-403">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="4e959-404">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="4e959-404">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="4e959-405">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="4e959-405">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="4e959-406">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="4e959-406">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="4e959-407">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="4e959-407">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="4e959-408">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="4e959-408">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="4e959-409">使用 PowerShell 删除 app_offline.html  （需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="4e959-409">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="4e959-410">数据保护</span><span class="sxs-lookup"><span data-stu-id="4e959-410">Data protection</span></span>

<span data-ttu-id="4e959-411">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="4e959-411">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="4e959-412">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="4e959-412">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="4e959-413">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="4e959-413">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="4e959-414">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="4e959-414">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="4e959-415">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="4e959-415">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="4e959-416">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="4e959-416">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="4e959-417">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="4e959-417">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="4e959-418">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="4e959-418">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="4e959-419">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一  ：</span><span class="sxs-lookup"><span data-stu-id="4e959-419">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="4e959-420">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="4e959-420">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="4e959-421">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="4e959-421">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="4e959-422">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="4e959-422">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="4e959-423">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="4e959-423">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="4e959-424">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="4e959-424">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="4e959-425">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="4e959-425">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="4e959-426">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="4e959-426">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="4e959-427">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="4e959-427">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="4e959-428">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="4e959-428">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="4e959-429">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="4e959-429">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="4e959-430">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="4e959-430">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="4e959-431">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="4e959-431">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="4e959-432">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="4e959-432">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="4e959-433">此设置位于应用池“高级设置”下的“进程模型”部分   。</span><span class="sxs-lookup"><span data-stu-id="4e959-433">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="4e959-434">将“加载用户配置文件”设置为 `True`  。</span><span class="sxs-lookup"><span data-stu-id="4e959-434">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="4e959-435">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="4e959-435">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="4e959-436">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="4e959-436">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="4e959-437">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="4e959-437">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="4e959-438">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4e959-438">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="4e959-439">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4e959-439">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="4e959-440">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="4e959-440">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="4e959-441">导航到 %windir%/system32/inetsrv/config  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4e959-441">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="4e959-442">打开 applicationHost.config  文件。</span><span class="sxs-lookup"><span data-stu-id="4e959-442">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="4e959-443">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="4e959-443">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="4e959-444">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4e959-444">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="4e959-445">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="4e959-445">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="4e959-446">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="4e959-446">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="4e959-447">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="4e959-447">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="4e959-448">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="4e959-448">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="4e959-449">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="4e959-449">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="4e959-450">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="4e959-450">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="4e959-451">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="4e959-451">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="4e959-452">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="4e959-452">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="4e959-453">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="4e959-453">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="4e959-454">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="4e959-454">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="4e959-455">有关更多信息，请参见<xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="4e959-455">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="4e959-456">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="4e959-456">Virtual Directories</span></span>

<span data-ttu-id="4e959-457">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="4e959-457">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="4e959-458">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="4e959-458">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="4e959-459">子应用程序</span><span class="sxs-lookup"><span data-stu-id="4e959-459">Sub-applications</span></span>

<span data-ttu-id="4e959-460">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="4e959-460">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="4e959-461">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="4e959-461">The sub-app's path becomes part of the root app's URL.</span></span>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4e959-462">子应用不应将 ASP.NET Core 模块作为处理程序包含在其中。</span><span class="sxs-lookup"><span data-stu-id="4e959-462">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="4e959-463">如果在子应用的 web.config  文件中将该模块添加为处理程序，则在尝试浏览子应用时会收到“500.19 内部服务器错误”  ，即引用错误的配置文件。</span><span class="sxs-lookup"><span data-stu-id="4e959-463">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="4e959-464">以下示例显示 ASP.NET Core 子应用的已发布 web.config  文件：</span><span class="sxs-lookup"><span data-stu-id="4e959-464">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="4e959-465">在 ASP.NET Core 应用下托管非 ASP.NET Core 子应用时，需在子应用的 web.config 文件中显式删除继承的处理程序  ：</span><span class="sxs-lookup"><span data-stu-id="4e959-465">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

::: moniker-end

<span data-ttu-id="4e959-466">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="4e959-466">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="4e959-467">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="4e959-467">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="4e959-468">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="4e959-468">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="4e959-469">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="4e959-469">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="4e959-470">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="4e959-470">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="4e959-471">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="4e959-471">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="4e959-472">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”  响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="4e959-472">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="4e959-473">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="4e959-473">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="4e959-474">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="4e959-474">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="4e959-475">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中   。</span><span class="sxs-lookup"><span data-stu-id="4e959-475">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="4e959-476">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="4e959-476">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="4e959-477">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。 </span><span class="sxs-lookup"><span data-stu-id="4e959-477">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="4e959-478">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。   </span><span class="sxs-lookup"><span data-stu-id="4e959-478">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="4e959-479">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-479">Select **OK**.</span></span>

<span data-ttu-id="4e959-480">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="4e959-480">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="4e959-481">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="4e959-481">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="4e959-482">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="4e959-482">Configuration of IIS with web.config</span></span>

<span data-ttu-id="4e959-483">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config  的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="4e959-483">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="4e959-484">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="4e959-484">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="4e959-485">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config  文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="4e959-485">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="4e959-486">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="4e959-486">For more information, see the following topics:</span></span>

* [<span data-ttu-id="4e959-487">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="4e959-487">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="4e959-488">若要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”  部分。</span><span class="sxs-lookup"><span data-stu-id="4e959-488">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="4e959-489">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="4e959-489">Configuration sections of web.config</span></span>

<span data-ttu-id="4e959-490">ASP.NET Core 应用不会使用 web.config  中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="4e959-490">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="4e959-491">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="4e959-491">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="4e959-492">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="4e959-492">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="4e959-493">应用程序池</span><span class="sxs-lookup"><span data-stu-id="4e959-493">Application Pools</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4e959-494">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="4e959-494">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="4e959-495">进程内托管 &ndash; 应用都需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="4e959-495">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="4e959-496">进程外托管 &ndash; 建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="4e959-496">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="4e959-497">IIS“添加网站”对话框默认为每应用一个应用池  。</span><span class="sxs-lookup"><span data-stu-id="4e959-497">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="4e959-498">提供了站点名称时，该文本会自动传输到“应用程序池”文本框   。</span><span class="sxs-lookup"><span data-stu-id="4e959-498">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="4e959-499">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="4e959-499">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4e959-500">在服务器上托管多个网站时，建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="4e959-500">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="4e959-501">IIS“添加网站”对话框默认执行此配置  。</span><span class="sxs-lookup"><span data-stu-id="4e959-501">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="4e959-502">提供了站点名称时，该文本会自动传输到“应用程序池”文本框   。</span><span class="sxs-lookup"><span data-stu-id="4e959-502">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="4e959-503">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="4e959-503">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

## <a name="application-pool-identity"></a><span data-ttu-id="4e959-504">应用程序池标识</span><span class="sxs-lookup"><span data-stu-id="4e959-504">Application Pool Identity</span></span>

<span data-ttu-id="4e959-505">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="4e959-505">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="4e959-506">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="4e959-506">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="4e959-507">在 IIS 管理控制台中，确保应用池“高级设置”下的“标识”设置为使用 ApplicationPoolIdentity    ：</span><span class="sxs-lookup"><span data-stu-id="4e959-507">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="4e959-509">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="4e959-509">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="4e959-510">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="4e959-510">Resources can be secured using this identity.</span></span> <span data-ttu-id="4e959-511">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="4e959-511">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="4e959-512">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="4e959-512">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="4e959-513">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="4e959-513">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="4e959-514">右键单击该目录，然后选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-514">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="4e959-515">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮    。</span><span class="sxs-lookup"><span data-stu-id="4e959-515">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="4e959-516">选择“位置”按钮，并确保该系统处于选中状态  。</span><span class="sxs-lookup"><span data-stu-id="4e959-516">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="4e959-517">在“输入要选择的对象名称”  区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="4e959-517">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="4e959-518">选择“检查名称”按钮  。</span><span class="sxs-lookup"><span data-stu-id="4e959-518">Select the **Check Names** button.</span></span> <span data-ttu-id="4e959-519">有关 DefaultAppPool  ，请检查使用 IIS AppPool\DefaultAppPool  的名称。</span><span class="sxs-lookup"><span data-stu-id="4e959-519">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="4e959-520">当选择“检查名称”  按钮时，对象名称区域中会显示 DefaultAppPool  的值。</span><span class="sxs-lookup"><span data-stu-id="4e959-520">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="4e959-521">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="4e959-521">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="4e959-522">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="4e959-522">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="4e959-524">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-524">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="4e959-526">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="4e959-526">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="4e959-527">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="4e959-527">Provide additional permissions as needed.</span></span>

<span data-ttu-id="4e959-528">也可使用 ICACLS  工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="4e959-528">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="4e959-529">以 DefaultAppPool  为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="4e959-529">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="4e959-530">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="4e959-530">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="4e959-531">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="4e959-531">HTTP/2 support</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4e959-532">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="4e959-532">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="4e959-533">进程内</span><span class="sxs-lookup"><span data-stu-id="4e959-533">In-process</span></span>
  * <span data-ttu-id="4e959-534">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4e959-534">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="4e959-535">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="4e959-535">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="4e959-536">进程外</span><span class="sxs-lookup"><span data-stu-id="4e959-536">Out-of-process</span></span>
  * <span data-ttu-id="4e959-537">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4e959-537">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="4e959-538">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="4e959-538">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="4e959-539">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="4e959-539">TLS 1.2 or later connection</span></span>

<span data-ttu-id="4e959-540">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="4e959-540">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="4e959-541">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="4e959-541">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="4e959-542">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="4e959-542">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4e959-543">满足以下基本要求的进程外部署支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="4e959-543">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="4e959-544">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4e959-544">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="4e959-545">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="4e959-545">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="4e959-546">目标框架：不适用于进程外部署，因为 HTTP/2 连接完全由 IIS 处理。</span><span class="sxs-lookup"><span data-stu-id="4e959-546">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="4e959-547">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="4e959-547">TLS 1.2 or later connection</span></span>

<span data-ttu-id="4e959-548">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="4e959-548">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="4e959-549">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="4e959-549">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="4e959-550">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="4e959-550">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="4e959-551">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="4e959-551">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="4e959-552">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="4e959-552">CORS preflight requests</span></span>

<span data-ttu-id="4e959-553">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。 </span><span class="sxs-lookup"><span data-stu-id="4e959-553">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="4e959-554">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="4e959-554">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="4e959-555">若要了解如何在 web.config  中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="4e959-555">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="4e959-556">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="4e959-556">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="4e959-557">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="4e959-557">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="4e959-558">[应用程序初始化模块](#application-initialization-module) &ndash; 应用的托管[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)可配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="4e959-558">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="4e959-559">[空闲超时](#idle-timeout) &ndash; 可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段期间不超时。</span><span class="sxs-lookup"><span data-stu-id="4e959-559">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="4e959-560">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="4e959-560">Application Initialization Module</span></span>

<span data-ttu-id="4e959-561">适用于托管在进程内和进程外的应用。 </span><span class="sxs-lookup"><span data-stu-id="4e959-561">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="4e959-562">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="4e959-562">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="4e959-563">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="4e959-563">The request triggers the app to start.</span></span> <span data-ttu-id="4e959-564">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="4e959-564">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="4e959-565">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="4e959-565">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="4e959-566">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="4e959-566">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="4e959-567">导航到“控制面板”>“程序”>“程序和功能”>“启用或禁用 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="4e959-567">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="4e959-568">打开“Internet Information Services”  >“万维网服务”  >“应用程序开发功能”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-568">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="4e959-569">选中“应用程序初始化”  的复选框。</span><span class="sxs-lookup"><span data-stu-id="4e959-569">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="4e959-570">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="4e959-570">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="4e959-571">打开“添加角色和功能向导”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-571">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="4e959-572">在“选择角色服务”面板中，打开“应用程序开发”节点   。</span><span class="sxs-lookup"><span data-stu-id="4e959-572">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="4e959-573">选中“应用程序初始化”  的复选框。</span><span class="sxs-lookup"><span data-stu-id="4e959-573">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="4e959-574">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="4e959-574">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="4e959-575">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="4e959-575">Using IIS Manager:</span></span>

  1. <span data-ttu-id="4e959-576">在“连接”  面板中选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-576">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="4e959-577">在列表中右键单击应用的应用池，并选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-577">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="4e959-578">默认的“启动模式”为“按需”   。</span><span class="sxs-lookup"><span data-stu-id="4e959-578">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="4e959-579">将“启动模式”  设置为“始终运行”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-579">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="4e959-580">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-580">Select **OK**.</span></span>
  1. <span data-ttu-id="4e959-581">打开“连接”  面板中的“网站”  节点。</span><span class="sxs-lookup"><span data-stu-id="4e959-581">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="4e959-582">右键单击应用，并选择“管理网站”>“高级设置”   。</span><span class="sxs-lookup"><span data-stu-id="4e959-582">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="4e959-583">默认的“预加载已启用”设置为“False”   。</span><span class="sxs-lookup"><span data-stu-id="4e959-583">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="4e959-584">将“预加载已启用”  设置为“True”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-584">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="4e959-585">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-585">Select **OK**.</span></span>

* <span data-ttu-id="4e959-586">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中   ：</span><span class="sxs-lookup"><span data-stu-id="4e959-586">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="4e959-587">空闲超时</span><span class="sxs-lookup"><span data-stu-id="4e959-587">Idle Timeout</span></span>

<span data-ttu-id="4e959-588">仅适用于托管在进程内的应用。 </span><span class="sxs-lookup"><span data-stu-id="4e959-588">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="4e959-589">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="4e959-589">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="4e959-590">在“连接”  面板中选择“应用程序池”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-590">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="4e959-591">在列表中右键单击应用的应用池，并选择“高级设置”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-591">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="4e959-592">默认的“空闲超时(分钟)”为“20”分钟   。</span><span class="sxs-lookup"><span data-stu-id="4e959-592">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="4e959-593">将“空闲超时(分钟)”设置为“0”（零）   。</span><span class="sxs-lookup"><span data-stu-id="4e959-593">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="4e959-594">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="4e959-594">Select **OK**.</span></span>
1. <span data-ttu-id="4e959-595">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="4e959-595">Recycle the worker process.</span></span>

<span data-ttu-id="4e959-596">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="4e959-596">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="4e959-597">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="4e959-597">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="4e959-598">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="4e959-598">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="4e959-599">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="4e959-599">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="4e959-600">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="4e959-600">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="4e959-601">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="4e959-601">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="4e959-602">[应用程序池的进程模型设置 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="4e959-602">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

::: moniker-end

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="4e959-603">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="4e959-603">Deployment resources for IIS administrators</span></span>

<span data-ttu-id="4e959-604">在 IIS 文档中深入了解 IIS。</span><span class="sxs-lookup"><span data-stu-id="4e959-604">Learn about IIS in-depth in the IIS documentation.</span></span>  
[<span data-ttu-id="4e959-605">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="4e959-605">IIS documentation</span></span>](/iis)

<span data-ttu-id="4e959-606">了解 .NET Core 应用部署模型。</span><span class="sxs-lookup"><span data-stu-id="4e959-606">Learn about .NET Core app deployment models.</span></span>  
[<span data-ttu-id="4e959-607">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="4e959-607">.NET Core application deployment</span></span>](/dotnet/core/deploying/)

<span data-ttu-id="4e959-608">了解 ASP.NET Core 模块，包括配置指南。</span><span class="sxs-lookup"><span data-stu-id="4e959-608">Learn about the ASP.NET Core Module, including configuration guidance.</span></span>  
<xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="4e959-609">了解已发布的 ASP.NET Core 应用的目录结构。</span><span class="sxs-lookup"><span data-stu-id="4e959-609">Learn about the directory structure of published ASP.NET Core apps.</span></span>  
[<span data-ttu-id="4e959-610">目录结构</span><span class="sxs-lookup"><span data-stu-id="4e959-610">Directory structure</span></span>](xref:host-and-deploy/directory-structure)

<span data-ttu-id="4e959-611">了解适用于 ASP.NET Core 应用的活动和非活动 IIS 模块以及如何管理 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="4e959-611">Discover active and inactive IIS modules for ASP.NET Core apps and how to manage IIS modules.</span></span>  
[<span data-ttu-id="4e959-612">IIS 模块</span><span class="sxs-lookup"><span data-stu-id="4e959-612">IIS modules</span></span>](xref:host-and-deploy/iis/modules)

<span data-ttu-id="4e959-613">了解如何诊断 ASP.NET Core 应用的 IIS 部署的问题。</span><span class="sxs-lookup"><span data-stu-id="4e959-613">Learn how to diagnose problems with IIS deployments of ASP.NET Core apps.</span></span>  
[<span data-ttu-id="4e959-614">疑难解答</span><span class="sxs-lookup"><span data-stu-id="4e959-614">Troubleshoot</span></span>](xref:test/troubleshoot-azure-iis)

<span data-ttu-id="4e959-615">了解在 IIS 上托管 ASP.NET Core 应用的常见错误。</span><span class="sxs-lookup"><span data-stu-id="4e959-615">Distinguish common errors when hosting ASP.NET Core apps on IIS.</span></span>  
[<span data-ttu-id="4e959-616">Azure 应用服务和 IIS 的常见错误参考</span><span class="sxs-lookup"><span data-stu-id="4e959-616">Common errors reference for Azure App Service and IIS</span></span>](xref:host-and-deploy/azure-iis-errors-reference)

## <a name="additional-resources"></a><span data-ttu-id="4e959-617">其他资源</span><span class="sxs-lookup"><span data-stu-id="4e959-617">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="4e959-618">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="4e959-618">Introduction to ASP.NET Core</span></span>](xref:index)
* [<span data-ttu-id="4e959-619">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="4e959-619">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="4e959-620">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="4e959-620">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="4e959-621">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="4e959-621">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>
