---
title: ASP.NET Core 中的 Web 服务器实现
author: rick-anderson
description: 发现适用于 ASP.NET Core 的 Web 服务器 Kestrel 和 HTTP.sys。 了解如何选择服务器以及何时使用反向代理服务器。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/index
ms.openlocfilehash: fb9ba7cd4fe7ce805374dd802cc7ba4258d52527
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016747"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="e7fac-104">ASP.NET Core 中的 Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="e7fac-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="e7fac-105">作者：[Tom Dykstra](https://github.com/tdykstra)、 [Steve Smith](https://ardalis.com/)、[Stephen Halter](https://twitter.com/halter73) 和 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="e7fac-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="e7fac-106">ASP.NET Core 应用与进程内 HTTP 服务器实现一起运行。</span><span class="sxs-lookup"><span data-stu-id="e7fac-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="e7fac-107">该服务器实现侦听 HTTP 请求，并以组成 <xref:Microsoft.AspNetCore.Http.HttpContext> 的[请求功能](xref:fundamentals/request-features)集形式，将它们呈现给应用。</span><span class="sxs-lookup"><span data-stu-id="e7fac-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="e7fac-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e7fac-108">Kestrel</span></span>

<span data-ttu-id="e7fac-109">Kestrel 是 ASP.NET Core 项目模板指定的默认 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="e7fac-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="e7fac-110">使用 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="e7fac-110">Use Kestrel:</span></span>

* <span data-ttu-id="e7fac-111">本身作为边缘服务器，处理直接来自网络（包括 Internet）的请求。</span><span class="sxs-lookup"><span data-stu-id="e7fac-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="e7fac-113">与反向代理服务器\*\*（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="e7fac-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="e7fac-114">反向代理服务器接收来自 Internet 的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e7fac-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="e7fac-116">无论托管配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="e7fac-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="e7fac-117">有关 Kestrel 配置指南和何时在反向代理配置中使用 Kestrel 的信息，请参阅 <xref:fundamentals/servers/kestrel>。</span><span class="sxs-lookup"><span data-stu-id="e7fac-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="e7fac-118">Windows</span><span class="sxs-lookup"><span data-stu-id="e7fac-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="e7fac-119">ASP.NET Core 随附以下组件：</span><span class="sxs-lookup"><span data-stu-id="e7fac-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="e7fac-120">[Kestrel 服务器](xref:fundamentals/servers/kestrel)是默认跨平台 HTTP 服务器实现。</span><span class="sxs-lookup"><span data-stu-id="e7fac-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="e7fac-121">IIS HTTP 服务器是 IIS 的[进程内服务器](#hosting-models)。</span><span class="sxs-lookup"><span data-stu-id="e7fac-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="e7fac-122">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)是仅用于 Windows 的 HTTP 服务器，它基于 [HTTP.sys 核心驱动程序和 HTTP 服务器 API](/windows/desktop/Http/http-api-start-page)。</span><span class="sxs-lookup"><span data-stu-id="e7fac-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="e7fac-123">使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用会在以下其中一个进程中运行：</span><span class="sxs-lookup"><span data-stu-id="e7fac-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="e7fac-124">在使用 IIS HTTP 服务器的 IIS 工作进程（[进程内托管模型](#hosting-models)）相同的进程中。</span><span class="sxs-lookup"><span data-stu-id="e7fac-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="e7fac-125">“进程内”\*\* 建议的配置。</span><span class="sxs-lookup"><span data-stu-id="e7fac-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="e7fac-126">在独立于 IIS 工作进程（[进程外托管模型](#hosting-models)）和 [Kestrel 服务器](#kestrel)的进程中。</span><span class="sxs-lookup"><span data-stu-id="e7fac-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="e7fac-127">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)是本机 IIS 模块，用于处理 IIS 和进程内 IIS HTTP 服务器或 Kestrel 之间的本机 IIS 请求。</span><span class="sxs-lookup"><span data-stu-id="e7fac-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="e7fac-128">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="e7fac-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="e7fac-129">托管模型</span><span class="sxs-lookup"><span data-stu-id="e7fac-129">Hosting models</span></span>

<span data-ttu-id="e7fac-130">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="e7fac-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="e7fac-131">进程内承载相较进程外承载提供更优的性能，因为请求并不通过环回适配器进行代理，环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="e7fac-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="e7fac-132">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="e7fac-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e7fac-133">通过进程外托管，ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，而由模块来处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="e7fac-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="e7fac-134">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="e7fac-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e7fac-135">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="e7fac-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e7fac-136">有关详细信息和配置指南，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="e7fac-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="e7fac-137">macOS</span><span class="sxs-lookup"><span data-stu-id="e7fac-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="e7fac-138">ASP.NET Core 随附 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，这是默认跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="e7fac-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="e7fac-139">Linux</span><span class="sxs-lookup"><span data-stu-id="e7fac-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="e7fac-140">ASP.NET Core 随附 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，这是默认跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="e7fac-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="e7fac-141">Windows</span><span class="sxs-lookup"><span data-stu-id="e7fac-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="e7fac-142">ASP.NET Core 随附以下组件：</span><span class="sxs-lookup"><span data-stu-id="e7fac-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="e7fac-143">[Kestrel 服务器](xref:fundamentals/servers/kestrel)是默认跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="e7fac-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="e7fac-144">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)是仅用于 Windows 的 HTTP 服务器，它基于 [HTTP.sys 核心驱动程序和 HTTP 服务器 API](/windows/desktop/Http/http-api-start-page)。</span><span class="sxs-lookup"><span data-stu-id="e7fac-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="e7fac-145">使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用在独立于 IIS 工作进程（进程外）\*\* 和 [Kestrel 服务器](#kestrel)的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="e7fac-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="e7fac-146">由于 ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，因此该模块会处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="e7fac-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="e7fac-147">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="e7fac-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e7fac-148">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="e7fac-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e7fac-149">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="e7fac-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模块](_static/ancm-outofprocess.png)

<span data-ttu-id="e7fac-151">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="e7fac-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="e7fac-152">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="e7fac-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="e7fac-153">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e7fac-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="e7fac-154">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="e7fac-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="e7fac-155">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="e7fac-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="e7fac-156">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="e7fac-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="e7fac-157">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="e7fac-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e7fac-158">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="e7fac-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e7fac-159">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e7fac-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="e7fac-160">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="e7fac-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="e7fac-161">有关 IIS 和 ASP.NET Core 模块的配置指南，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="e7fac-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="e7fac-162">macOS</span><span class="sxs-lookup"><span data-stu-id="e7fac-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="e7fac-163">ASP.NET Core 随附 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，这是默认跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="e7fac-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="e7fac-164">Linux</span><span class="sxs-lookup"><span data-stu-id="e7fac-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="e7fac-165">ASP.NET Core 随附 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，这是默认跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="e7fac-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="e7fac-166">Nginx 与 Kestrel</span><span class="sxs-lookup"><span data-stu-id="e7fac-166">Nginx with Kestrel</span></span>

<span data-ttu-id="e7fac-167">若要了解如何在 Linux 上使用 Nginx 作为 Kestrel 的反向代理服务器，请参阅 <xref:host-and-deploy/linux-nginx>。</span><span class="sxs-lookup"><span data-stu-id="e7fac-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="e7fac-168">Apache 与 Kestrel</span><span class="sxs-lookup"><span data-stu-id="e7fac-168">Apache with Kestrel</span></span>

<span data-ttu-id="e7fac-169">若要了解如何在 Linux 上使用 Apache 作为 Kestrel 的反向代理服务器，请参阅 <xref:host-and-deploy/linux-apache>。</span><span class="sxs-lookup"><span data-stu-id="e7fac-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="e7fac-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7fac-170">HTTP.sys</span></span>

<span data-ttu-id="e7fac-171">如果 ASP.NET Core 应用在 Windows 上运行，则 HTTP.sys 是 Kestrel 的替代选项。</span><span class="sxs-lookup"><span data-stu-id="e7fac-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="e7fac-172">为了获得最佳性能，通常建议使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="e7fac-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="e7fac-173">在应用向 Internet 公开且所需功能受 HTTP.sys（而不是 Kestrel）支持的方案中，可以使用 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="e7fac-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="e7fac-174">有关详细信息，请参阅 <xref:fundamentals/servers/httpsys>。</span><span class="sxs-lookup"><span data-stu-id="e7fac-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="e7fac-176">对于仅向内部网络公开的应用，HTTP.sys 同样适用。</span><span class="sxs-lookup"><span data-stu-id="e7fac-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="e7fac-178">有关 HTTP.sys 的配置指南，请参阅 <xref:fundamentals/servers/httpsys>。</span><span class="sxs-lookup"><span data-stu-id="e7fac-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="e7fac-179">ASP.NET Core 服务器基础结构</span><span class="sxs-lookup"><span data-stu-id="e7fac-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="e7fac-180">`Startup.Configure` 方法中提供的 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 公开了类型 <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> 的 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> 属性。</span><span class="sxs-lookup"><span data-stu-id="e7fac-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="e7fac-181">Kestrel 和 HTTP.sys 各自仅公开单个功能，即 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>，但是不同的服务器实现可能公开其他功能。</span><span class="sxs-lookup"><span data-stu-id="e7fac-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="e7fac-182">`IServerAddressesFeature` 可用于查找服务器实现在运行时绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="e7fac-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="e7fac-183">自定义服务器</span><span class="sxs-lookup"><span data-stu-id="e7fac-183">Custom servers</span></span>

<span data-ttu-id="e7fac-184">如果内置服务器无法满足应用需求，可以创建一个自定义服务器实现。</span><span class="sxs-lookup"><span data-stu-id="e7fac-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="e7fac-185">[.NET 的开放 Web 接口 (OWIN) 指南](xref:fundamentals/owin) 演示了如何编写基于 [Nowin](https://github.com/Bobris/Nowin) 的 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实现。</span><span class="sxs-lookup"><span data-stu-id="e7fac-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="e7fac-186">只有应用使用的功能接口需要实现，但至少必须支持 <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> 和 <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>。</span><span class="sxs-lookup"><span data-stu-id="e7fac-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="e7fac-187">服务器启动</span><span class="sxs-lookup"><span data-stu-id="e7fac-187">Server startup</span></span>

<span data-ttu-id="e7fac-188">集成开发环境 (IDE) 或编辑器启动以下应用时，会启动服务器：</span><span class="sxs-lookup"><span data-stu-id="e7fac-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="e7fac-189">[Visual Studio](https://visualstudio.microsoft.com)：可使用启动配置文件通过 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)或控制台来启动应用和服务器。</span><span class="sxs-lookup"><span data-stu-id="e7fac-189">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="e7fac-190">[Visual Studio Code](https://code.visualstudio.com/)：由 [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode) 通过激活 CoreCLR 调试程序来启动应用和服务器。</span><span class="sxs-lookup"><span data-stu-id="e7fac-190">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="e7fac-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)：由 [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) 启动应用和服务器。</span><span class="sxs-lookup"><span data-stu-id="e7fac-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="e7fac-192">从项目文件夹中的命令提示符启动应用时，[dotnet run](/dotnet/core/tools/dotnet-run) 会启动该应用和服务器（仅 Kestrel 和 HTTP.sys）。</span><span class="sxs-lookup"><span data-stu-id="e7fac-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="e7fac-193">可通过 `-c|--configuration` 选项指定此配置，该选项设置为 `Debug`（默认值）或 `Release`。</span><span class="sxs-lookup"><span data-stu-id="e7fac-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="e7fac-194">使用 `dotnet run` 或使用工具中内置的调试程序（如 Visual Studio）启动应用时，launchSettings.json 文件会提供配置\*\*。</span><span class="sxs-lookup"><span data-stu-id="e7fac-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="e7fac-195">如果启动配置文件位于 launchSettings.json 文件中，请结合使用 `--launch-profile {PROFILE NAME}` 选项和 `dotnet run` 命令或在 Visual Studio 中选择配置文件\*\*。</span><span class="sxs-lookup"><span data-stu-id="e7fac-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="e7fac-196">有关详细信息，请参阅 [dotnet run](/dotnet/core/tools/dotnet-run) 和 [.NET Core 分发打包](/dotnet/core/build/distribution-packaging)。</span><span class="sxs-lookup"><span data-stu-id="e7fac-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="e7fac-197">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="e7fac-197">HTTP/2 support</span></span>

<span data-ttu-id="e7fac-198">以下部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="e7fac-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="e7fac-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e7fac-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="e7fac-200">操作系统</span><span class="sxs-lookup"><span data-stu-id="e7fac-200">Operating system</span></span>
    * <span data-ttu-id="e7fac-201">Windows Server 2016/Windows 10 或更高版本&dagger;</span><span class="sxs-lookup"><span data-stu-id="e7fac-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="e7fac-202">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="e7fac-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="e7fac-203">macOS 的未来版本将支持 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="e7fac-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="e7fac-204">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e7fac-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="e7fac-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7fac-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="e7fac-206">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e7fac-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="e7fac-207">目标框架：不适用于 HTTP.sys 部署。</span><span class="sxs-lookup"><span data-stu-id="e7fac-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="e7fac-208">IIS（进程内）</span><span class="sxs-lookup"><span data-stu-id="e7fac-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="e7fac-209">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e7fac-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e7fac-210">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e7fac-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="e7fac-211">IIS（进程外）</span><span class="sxs-lookup"><span data-stu-id="e7fac-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="e7fac-212">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e7fac-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e7fac-213">面向公众的边缘服务器连接使用 HTTP/2，但与 Kestrel 的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e7fac-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="e7fac-214">目标框架：不适用于 IIS 进程外部署。</span><span class="sxs-lookup"><span data-stu-id="e7fac-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="e7fac-215">&dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="e7fac-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="e7fac-216">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="e7fac-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="e7fac-217">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="e7fac-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="e7fac-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7fac-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="e7fac-219">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e7fac-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="e7fac-220">目标框架：不适用于 HTTP.sys 部署。</span><span class="sxs-lookup"><span data-stu-id="e7fac-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="e7fac-221">IIS（进程外）</span><span class="sxs-lookup"><span data-stu-id="e7fac-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="e7fac-222">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="e7fac-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e7fac-223">面向公众的边缘服务器连接使用 HTTP/2，但与 Kestrel 的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e7fac-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="e7fac-224">目标框架：不适用于 IIS 进程外部署。</span><span class="sxs-lookup"><span data-stu-id="e7fac-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="e7fac-225">HTTP/2 连接必须使用[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 和 TLS 1.2 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="e7fac-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="e7fac-226">有关详细信息，请参阅与服务器部署方案相关的主题。</span><span class="sxs-lookup"><span data-stu-id="e7fac-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7fac-227">其他资源</span><span class="sxs-lookup"><span data-stu-id="e7fac-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
