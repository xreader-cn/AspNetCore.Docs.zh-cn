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
ms.openlocfilehash: 7fe3e18b226061260d0c17220ba110bd61486b5f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754692"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="71ae6-103">使用 IIS 在 Windows 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71ae6-103">Host ASP.NET Core on Windows with IIS</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="71ae6-104">Internet Information Services (IIS) 是一种灵活、安全且可管理的 Web 服务器，用于托管 Web 应用（包括 ASP.NET Core）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-104">Internet Information Services (IIS) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="71ae6-105">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="71ae6-105">Supported platforms</span></span>

<span data-ttu-id="71ae6-106">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="71ae6-106">The following operating systems are supported:</span></span>

* <span data-ttu-id="71ae6-107">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-107">Windows 7 or later</span></span>
* <span data-ttu-id="71ae6-108">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-108">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="71ae6-109">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-109">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="71ae6-110">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="71ae6-110">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="71ae6-111">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="71ae6-111">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="71ae6-112">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="71ae6-112">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="71ae6-113">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="71ae6-113">Has 64-bit native dependencies.</span></span>

## <a name="install-the-aspnet-core-modulehosting-bundle"></a><span data-ttu-id="71ae6-114">安装 ASP.NET Core 模块/托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="71ae6-114">Install the ASP.NET Core Module/Hosting Bundle</span></span>

<span data-ttu-id="71ae6-115">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="71ae6-115">Download the installer using the following link:</span></span>

[<span data-ttu-id="71ae6-116">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="71ae6-116">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="71ae6-117">有关如何安装 ASP.NET Core Module 或不同版本的详细说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-117">For more details instructions on how to install the ASP.NET Core Module, or installing different versions, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

## <a name="get-started"></a><span data-ttu-id="71ae6-118">入门</span><span class="sxs-lookup"><span data-stu-id="71ae6-118">Get started</span></span>

<span data-ttu-id="71ae6-119">有关在 IIS 上托管网站的入门知识，请参阅[入门指南](xref:tutorials/publish-to-iis)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-119">For getting started with hosting a website on IIS, see our [getting started guide](xref:tutorials/publish-to-iis).</span></span>

<span data-ttu-id="71ae6-120">有关在 Azure 应用服务上托管网站的入门知识，请参阅[“部署到 Azure 应用服务”指南](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-120">For getting started with hosting a website on Azure App Services, see our [deploying to Azure App Service guide](xref:host-and-deploy/azure-apps/index).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="71ae6-121">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="71ae6-121">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="71ae6-122">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="71ae6-122">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="71ae6-123">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="71ae6-123">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="71ae6-124">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-124">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="71ae6-125">其他资源</span><span class="sxs-lookup"><span data-stu-id="71ae6-125">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="71ae6-126">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="71ae6-126">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="71ae6-127">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="71ae6-127">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="71ae6-128">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="71ae6-128">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="71ae6-129">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-129">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="71ae6-130">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="71ae6-130">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="71ae6-131">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="71ae6-131">Supported operating systems</span></span>

<span data-ttu-id="71ae6-132">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="71ae6-132">The following operating systems are supported:</span></span>

* <span data-ttu-id="71ae6-133">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-133">Windows 7 or later</span></span>
* <span data-ttu-id="71ae6-134">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-134">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="71ae6-135">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-135">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="71ae6-136">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-136">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="71ae6-137">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-137">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="71ae6-138">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-138">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="71ae6-139">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="71ae6-139">Supported platforms</span></span>

<span data-ttu-id="71ae6-140">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-140">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="71ae6-141">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="71ae6-141">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="71ae6-142">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="71ae6-142">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="71ae6-143">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="71ae6-143">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="71ae6-144">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="71ae6-144">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="71ae6-145">为 32 位 (x86) 发布的应用必须已为其 IIS 应用程序池启用 32 位。</span><span class="sxs-lookup"><span data-stu-id="71ae6-145">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="71ae6-146">有关详细信息，请参阅[创建 IIS 站点](#create-the-iis-site)部分。</span><span class="sxs-lookup"><span data-stu-id="71ae6-146">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="71ae6-147">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-147">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="71ae6-148">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-148">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="71ae6-149">托管模型</span><span class="sxs-lookup"><span data-stu-id="71ae6-149">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="71ae6-150">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="71ae6-150">In-process hosting model</span></span>

<span data-ttu-id="71ae6-151">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-151">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="71ae6-152">进程内承载相较进程外承载提供更优的性能，因为请求并不通过环回适配器进行代理，环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="71ae6-152">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="71ae6-153">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-153">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="71ae6-154">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="71ae6-154">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="71ae6-155">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="71ae6-155">Performs app initialization.</span></span>
  * <span data-ttu-id="71ae6-156">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-156">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="71ae6-157">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-157">Calls `Program.Main`.</span></span>
* <span data-ttu-id="71ae6-158">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="71ae6-158">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="71ae6-159">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="71ae6-159">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="71ae6-161">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-161">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="71ae6-162">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-162">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="71ae6-163">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-163">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="71ae6-164">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="71ae6-164">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="71ae6-165">在 IIS HTTP 服务器处理请求后：</span><span class="sxs-lookup"><span data-stu-id="71ae6-165">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="71ae6-166">请求被发送到 ASP.NET Core 中间件管道。</span><span class="sxs-lookup"><span data-stu-id="71ae6-166">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="71ae6-167">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="71ae6-167">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="71ae6-168">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="71ae6-168">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="71ae6-169">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="71ae6-169">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="71ae6-170">对于现有应用，进程内托管是可选功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-170">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="71ae6-171">ASP.NET Core Web 模板使用进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="71ae6-171">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="71ae6-172">`CreateDefaultBuilder` 添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例的方式是：调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 方法来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 和将应用托管在 IIS 工作进程（`w3wp.exe` 或 `iisexpress.exe`）内。</span><span class="sxs-lookup"><span data-stu-id="71ae6-172">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="71ae6-173">性能测试表明，与在进程外托管应用并将请求代理传入 [Kestrel](xref:fundamentals/servers/kestrel) 相比，在进程中托管 .NET Core 应用可提供明显更高的请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="71ae6-173">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="71ae6-174">作为单个文件可执行文件发布的应用无法由进程内托管模型加载。</span><span class="sxs-lookup"><span data-stu-id="71ae6-174">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="71ae6-175">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="71ae6-175">Out-of-process hosting model</span></span>

<span data-ttu-id="71ae6-176">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-176">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="71ae6-177">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-177">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="71ae6-178">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="71ae6-178">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="71ae6-179">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="71ae6-179">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="71ae6-181">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-181">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="71ae6-182">驱动程序将请求路由到网站的配置端口上的 IIS。</span><span class="sxs-lookup"><span data-stu-id="71ae6-182">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="71ae6-183">配置的端口通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-183">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="71ae6-184">此模块将该请求转发到应用的随机端口上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="71ae6-184">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="71ae6-185">随机端口不是 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="71ae6-185">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="71ae6-186">ASP.NET Core 模块在启动时通过环境变量指定端口。</span><span class="sxs-lookup"><span data-stu-id="71ae6-186">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="71ae6-187"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-187">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="71ae6-188">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-188">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="71ae6-189">此模块不支持 HTTPS 转发。</span><span class="sxs-lookup"><span data-stu-id="71ae6-189">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="71ae6-190">即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="71ae6-190">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="71ae6-191">Kestrel 从模块获取请求后，请求会被转发到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-191">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="71ae6-192">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="71ae6-192">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="71ae6-193">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="71ae6-193">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="71ae6-194">应用的响应传递回 IIS，IIS 将响应转发回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="71ae6-194">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="71ae6-195">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-195">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="71ae6-196">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-196">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="71ae6-197">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="71ae6-197">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="71ae6-198">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="71ae6-198">Enable the IISIntegration components</span></span>

<span data-ttu-id="71ae6-199">在 `CreateHostBuilder` 中生成主机 (`Program.cs`)，请调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="71ae6-199">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="71ae6-200">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/generic-host#default-builder-settings>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-200">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="71ae6-201">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="71ae6-201">IIS options</span></span>

<span data-ttu-id="71ae6-202">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="71ae6-202">**In-process hosting model**</span></span>

<span data-ttu-id="71ae6-203">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-203">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="71ae6-204">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="71ae6-204">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="71ae6-205">选项</span><span class="sxs-lookup"><span data-stu-id="71ae6-205">Option</span></span>                         | <span data-ttu-id="71ae6-206">默认</span><span class="sxs-lookup"><span data-stu-id="71ae6-206">Default</span></span> | <span data-ttu-id="71ae6-207">设置</span><span class="sxs-lookup"><span data-stu-id="71ae6-207">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="71ae6-208">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-208">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="71ae6-209">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="71ae6-209">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="71ae6-210">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-210">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="71ae6-211">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-211">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="71ae6-212">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="71ae6-212">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="71ae6-213">`HttpContext.Request` 和 `HttpContext.Response` 是否允许同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="71ae6-213">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="71ae6-214">获取或设置 `HttpRequest` 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="71ae6-214">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="71ae6-215">请注意，IIS 本身有限制 `maxAllowedContentLength`，这一限制将在 `IISServerOptions` 中设置 `MaxRequestBodySize` 之前进行处理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-215">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="71ae6-216">更改 `MaxRequestBodySize` 不会影响 `maxAllowedContentLength`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-216">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="71ae6-217">若要增加 `maxAllowedContentLength`，请在 `web.config` 中添加一个将 `maxAllowedContentLength` 设置为更高值的项。</span><span class="sxs-lookup"><span data-stu-id="71ae6-217">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="71ae6-218">有关更多详细信息，请参阅[配置](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-218">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="71ae6-219">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="71ae6-219">**Out-of-process hosting model**</span></span>

<span data-ttu-id="71ae6-220">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-220">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="71ae6-221">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="71ae6-221">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="71ae6-222">选项</span><span class="sxs-lookup"><span data-stu-id="71ae6-222">Option</span></span>                         | <span data-ttu-id="71ae6-223">默认</span><span class="sxs-lookup"><span data-stu-id="71ae6-223">Default</span></span> | <span data-ttu-id="71ae6-224">设置</span><span class="sxs-lookup"><span data-stu-id="71ae6-224">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="71ae6-225">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-225">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="71ae6-226">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="71ae6-226">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="71ae6-227">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-227">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="71ae6-228">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="71ae6-228">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="71ae6-229">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="71ae6-229">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="71ae6-230">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-230">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="71ae6-231">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="71ae6-231">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="71ae6-232">将 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块配置为转发：</span><span class="sxs-lookup"><span data-stu-id="71ae6-232">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="71ae6-233">方案 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-233">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="71ae6-234">发起请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="71ae6-234">Remote IP address where the request originated.</span></span>

<span data-ttu-id="71ae6-235">[IIS 集成中间件](#enable-the-iisintegration-components)配置转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-235">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="71ae6-236">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-236">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="71ae6-237">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-237">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="71ae6-238">`web.config` 文件</span><span class="sxs-lookup"><span data-stu-id="71ae6-238">`web.config` file</span></span>

<span data-ttu-id="71ae6-239">`web.config` 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-239">The `web.config` file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="71ae6-240">发布项目时，`web.config` 文件的创建、转换和发布是由 MSBuild 目标 (`_TransformWebConfig`) 处理的。</span><span class="sxs-lookup"><span data-stu-id="71ae6-240">Creating, transforming, and publishing the `web.config` file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="71ae6-241">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-241">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="71ae6-242">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="71ae6-242">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="71ae6-243">如果项目中没有 `web.config` 文件，则该文件是使用正确的 `processPath` 和 `arguments`（用于配置 ASP.NET Core 模块）创建的，并且已被移到[发布的输出](xref:host-and-deploy/directory-structure)中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-243">If a `web.config` file isn't present in the project, the file is created with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="71ae6-244">如果项目中没有 `web.config` 文件，则该文件是通过正确的 `processPath` 和 `arguments`（用于配置 ASP.NET Core 模块）转换的，并且已被移到发布的输出中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-244">If a `web.config` file is present in the project, the file is transformed with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="71ae6-245">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-245">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="71ae6-246">`web.config` 文件可能会提供控制活动 IIS 模块的额外 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-246">The `web.config` file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="71ae6-247">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="71ae6-247">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="71ae6-248">为了防止 Web SDK 转换 `web.config` 文件，请在项目文件中使用 `<IsTransformWebConfigDisabled>` 属性：</span><span class="sxs-lookup"><span data-stu-id="71ae6-248">To prevent the Web SDK from transforming the `web.config` file, use the `<IsTransformWebConfigDisabled>` property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="71ae6-249">禁用 Web SDK 对文件的转换时，`processPath` 和 `arguments` 应由开发人员手动设置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-249">When disabling the Web SDK from transforming the file, the `processPath` and `arguments` should be manually set by the developer.</span></span> <span data-ttu-id="71ae6-250">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-250">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="71ae6-251">`web.config` 文件位置</span><span class="sxs-lookup"><span data-stu-id="71ae6-251">`web.config` file location</span></span>

<span data-ttu-id="71ae6-252">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，`web.config` 文件必须存在于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-252">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the `web.config` file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="71ae6-253">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="71ae6-253">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="71ae6-254">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 `web.config` 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-254">The `web.config` file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="71ae6-255">敏感文件存在于应用的物理路径中，如 `{ASSEMBLY}.runtimeconfig.json`、`{ASSEMBLY}.xml`（XML 文档注释）和 `{ASSEMBLY}.deps.json`，其中 `{ASSEMBLY}` 占位符为程序集名称。</span><span class="sxs-lookup"><span data-stu-id="71ae6-255">Sensitive files exist on the app's physical path, such as `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (XML Documentation comments), and `{ASSEMBLY}.deps.json`, where the placeholder `{ASSEMBLY}` is the assembly name.</span></span> <span data-ttu-id="71ae6-256">如果有 `web.config` 文件且站点正常启动时，IIS 在收到敏感文件请求时不会提供这些敏感文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-256">When the `web.config` file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="71ae6-257">如果 `web.config` 文件缺失、名字错误或者无法将站点配置为正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-257">If the `web.config` file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="71ae6-258">`web.config` 文件必须始终存在于部署中、名称正确以及能够将站点配置为正常启动。切勿从生产部署中删除 `web.config` 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-258">**The `web.config` file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the `web.config` file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="71ae6-259">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="71ae6-259">Transform web.config</span></span>

<span data-ttu-id="71ae6-260">如果在发布时需要转换 `web.config`，请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-260">If you need to transform `web.config` on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="71ae6-261">你需要在发布时转换 `web.config`，以根据配置、配置文件或环境来设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="71ae6-261">You might need to transform `web.config` on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="71ae6-262">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="71ae6-262">IIS configuration</span></span>

<span data-ttu-id="71ae6-263">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="71ae6-263">**Windows Server operating systems**</span></span>

<span data-ttu-id="71ae6-264">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="71ae6-264">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="71ae6-265">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="71ae6-265">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="71ae6-266">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-266">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="71ae6-268">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="71ae6-268">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="71ae6-269">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="71ae6-269">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="71ae6-271">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-271">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="71ae6-272">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-272">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="71ae6-273">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-273">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="71ae6-274">有关详细信息，请参阅 [Windows 身份验证 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-274">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="71ae6-275">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-275">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="71ae6-276">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-276">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="71ae6-277">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-277">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="71ae6-278">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-278">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="71ae6-279">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-279">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="71ae6-280">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="71ae6-280">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="71ae6-281">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="71ae6-281">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="71ae6-282">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="71ae6-282">**Windows desktop operating systems**</span></span>

<span data-ttu-id="71ae6-283">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-283">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="71ae6-284">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="71ae6-284">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="71ae6-285">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-285">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="71ae6-286">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-286">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="71ae6-287">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="71ae6-287">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="71ae6-288">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="71ae6-288">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="71ae6-289">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-289">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="71ae6-290">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-290">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="71ae6-291">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-291">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="71ae6-292">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-292">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="71ae6-293">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-293">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="71ae6-294">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-294">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="71ae6-295">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-295">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="71ae6-296">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-296">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="71ae6-297">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-297">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="71ae6-298">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-298">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="71ae6-299">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="71ae6-299">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="71ae6-301">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="71ae6-301">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="71ae6-302">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="71ae6-302">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="71ae6-303">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-303">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="71ae6-304">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-304">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71ae6-305">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="71ae6-305">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="71ae6-306">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-306">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="71ae6-307">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-307">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="71ae6-308">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-308">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="71ae6-309">直接下载（当前版本）</span><span class="sxs-lookup"><span data-stu-id="71ae6-309">Direct download (current version)</span></span>

<span data-ttu-id="71ae6-310">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="71ae6-310">Download the installer using the following link:</span></span>

[<span data-ttu-id="71ae6-311">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="71ae6-311">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="71ae6-312">先前版本的安装程序</span><span class="sxs-lookup"><span data-stu-id="71ae6-312">Earlier versions of the installer</span></span>

<span data-ttu-id="71ae6-313">若要获取先前版本的安装程序：</span><span class="sxs-lookup"><span data-stu-id="71ae6-313">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="71ae6-314">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="71ae6-314">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="71ae6-315">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-315">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="71ae6-316">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-316">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="71ae6-317">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-317">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="71ae6-318">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-318">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="71ae6-319">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-319">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="71ae6-320">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="71ae6-320">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="71ae6-321">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-321">Run the installer on the server.</span></span> <span data-ttu-id="71ae6-322">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="71ae6-322">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="71ae6-323">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="71ae6-323">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="71ae6-324">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-324">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="71ae6-325">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-325">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="71ae6-326">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-326">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="71ae6-327">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-327">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="71ae6-328">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-328">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="71ae6-329">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="71ae6-329">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="71ae6-330">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-330">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="71ae6-331">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (`applicationHost.config`) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="71ae6-331">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="71ae6-332">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-332">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="71ae6-333">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-333">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="71ae6-334">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="71ae6-334">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="71ae6-335">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="71ae6-335">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="71ae6-336">ASP.NET Core 不采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="71ae6-336">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="71ae6-337">通过安装新的托管捆绑包升级共享框架后，请重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="71ae6-337">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="71ae6-338">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-338">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="71ae6-339">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-339">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="71ae6-340">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="71ae6-340">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="71ae6-341">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-341">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="71ae6-342">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="71ae6-342">The preferred method is to use WebPI.</span></span> <span data-ttu-id="71ae6-343">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-343">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="71ae6-344">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="71ae6-344">Create the IIS site</span></span>

1. <span data-ttu-id="71ae6-345">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-345">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="71ae6-346">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="71ae6-346">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="71ae6-347">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-347">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="71ae6-348">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-348">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="71ae6-349">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="71ae6-349">Right-click the **Sites** folder.</span></span> <span data-ttu-id="71ae6-350">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-350">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="71ae6-351">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-351">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="71ae6-352">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="71ae6-352">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="71ae6-354">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-354">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="71ae6-355">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="71ae6-355">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="71ae6-356">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="71ae6-356">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="71ae6-357">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="71ae6-357">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="71ae6-358">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="71ae6-358">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="71ae6-359">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-359">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="71ae6-360">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-360">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="71ae6-361">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-361">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="71ae6-362">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="71ae6-362">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="71ae6-364">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-364">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="71ae6-365">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载。</span><span class="sxs-lookup"><span data-stu-id="71ae6-365">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="71ae6-366">将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR)，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-366">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="71ae6-367">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-367">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="71ae6-368">*ASP.NET Core 2.2 或更高版本*：</span><span class="sxs-lookup"><span data-stu-id="71ae6-368">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="71ae6-369">对于使用 32 位 SDK 发布的 32 位 (x86) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，且该 SDK 使用[进程内托管模型](#in-process-hosting-model)，请为 32 位启用应用程序池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-369">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="71ae6-370">在 IIS 管理器中，导航到“连接”边栏中的“应用程序池” 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-370">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="71ae6-371">选择应用的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-371">Select the app's Application Pool.</span></span> <span data-ttu-id="71ae6-372">在“操作”边栏中，选择，“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-372">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="71ae6-373">将“启用 32 位应用程序”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-373">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="71ae6-374">对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-374">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="71ae6-375">在 IIS 管理器中，导航到“连接”边栏中的“应用程序池” 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-375">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="71ae6-376">选择应用的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-376">Select the app's Application Pool.</span></span> <span data-ttu-id="71ae6-377">在“操作”边栏中，选择，“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-377">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="71ae6-378">将“启用 32 位应用程序”设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-378">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="71ae6-379">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-379">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="71ae6-380">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-380">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="71ae6-381">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-381">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="71ae6-382">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-382">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="71ae6-383">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-383">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="71ae6-384">部署应用</span><span class="sxs-lookup"><span data-stu-id="71ae6-384">Deploy the app</span></span>

<span data-ttu-id="71ae6-385">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="71ae6-385">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="71ae6-386">建议使用[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)这种部署机制，不过将应用从项目的 `publish` 文件夹移到托管系统的部署文件夹还有几种选择。</span><span class="sxs-lookup"><span data-stu-id="71ae6-386">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's `publish` folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="71ae6-387">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-387">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="71ae6-388">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-388">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="71ae6-389">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="71ae6-389">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="71ae6-391">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-391">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="71ae6-392">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-392">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="71ae6-393">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-393">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="71ae6-394">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="71ae6-394">Alternatives to Web Deploy</span></span>

<span data-ttu-id="71ae6-395">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="71ae6-395">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="71ae6-396">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="71ae6-396">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="71ae6-397">浏览网站</span><span class="sxs-lookup"><span data-stu-id="71ae6-397">Browse the website</span></span>

<span data-ttu-id="71ae6-398">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-398">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="71ae6-399">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-399">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="71ae6-400">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="71ae6-400">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="71ae6-402">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="71ae6-402">Locked deployment files</span></span>

<span data-ttu-id="71ae6-403">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="71ae6-403">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="71ae6-404">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-404">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="71ae6-405">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="71ae6-405">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="71ae6-406">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-406">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="71ae6-407">`app_offline.htm` 文件位于 Web 应用目录的根路径下。</span><span class="sxs-lookup"><span data-stu-id="71ae6-407">An `app_offline.htm` file is placed at the root of the web app directory.</span></span> <span data-ttu-id="71ae6-408">如果该文件存在，ASP.NET Core 模块在部署期间正常关闭应用并提供 `app_offline.htm` 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-408">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the `app_offline.htm` file during the deployment.</span></span> <span data-ttu-id="71ae6-409">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-409">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="71ae6-410">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-410">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="71ae6-411">使用 PowerShell 删除 `app_offline.htm`（要求 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="71ae6-411">Use PowerShell to drop `app_offline.htm` (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm
  ```

## <a name="data-protection"></a><span data-ttu-id="71ae6-412">数据保护</span><span class="sxs-lookup"><span data-stu-id="71ae6-412">Data protection</span></span>

<span data-ttu-id="71ae6-413">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-413">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="71ae6-414">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-414">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="71ae6-415">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="71ae6-415">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="71ae6-416">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-416">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="71ae6-417">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="71ae6-417">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="71ae6-418">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="71ae6-418">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="71ae6-419">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="71ae6-419">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="71ae6-420">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-420">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="71ae6-421">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="71ae6-421">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="71ae6-422">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="71ae6-422">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="71ae6-423">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-423">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="71ae6-424">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="71ae6-424">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="71ae6-425">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-425">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="71ae6-426">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="71ae6-426">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="71ae6-427">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="71ae6-427">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="71ae6-428">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="71ae6-428">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="71ae6-429">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="71ae6-429">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="71ae6-430">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="71ae6-430">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="71ae6-431">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="71ae6-431">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="71ae6-432">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-432">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="71ae6-433">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-433">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="71ae6-434">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="71ae6-434">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="71ae6-435">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-435">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="71ae6-436">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-436">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="71ae6-437">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="71ae6-437">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="71ae6-438">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-438">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="71ae6-439">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-439">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="71ae6-440">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-440">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="71ae6-441">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-441">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="71ae6-442">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="71ae6-442">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="71ae6-443">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="71ae6-443">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="71ae6-444">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-444">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="71ae6-445">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="71ae6-445">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="71ae6-446">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-446">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="71ae6-447">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="71ae6-447">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="71ae6-448">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-448">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="71ae6-449">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="71ae6-449">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="71ae6-450">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-450">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="71ae6-451">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-451">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="71ae6-452">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="71ae6-452">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="71ae6-453">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="71ae6-453">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="71ae6-454">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-454">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="71ae6-455">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="71ae6-455">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="71ae6-456">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-456">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="71ae6-457">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-457">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="71ae6-458">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="71ae6-458">Virtual Directories</span></span>

<span data-ttu-id="71ae6-459">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-459">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="71ae6-460">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-460">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="71ae6-461">子应用程序</span><span class="sxs-lookup"><span data-stu-id="71ae6-461">Sub-applications</span></span>

<span data-ttu-id="71ae6-462">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-462">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="71ae6-463">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="71ae6-463">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="71ae6-464">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="71ae6-464">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="71ae6-465">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="71ae6-465">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="71ae6-466">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-466">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="71ae6-467">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-467">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="71ae6-468">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-468">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="71ae6-469">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="71ae6-469">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="71ae6-470">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="71ae6-470">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="71ae6-471">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="71ae6-471">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="71ae6-472">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-472">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="71ae6-473">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-473">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="71ae6-474">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-474">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="71ae6-475">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-475">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="71ae6-476">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="71ae6-476">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="71ae6-477">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-477">Select **OK**.</span></span>

<span data-ttu-id="71ae6-478">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-478">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="71ae6-479">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-479">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="71ae6-480">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="71ae6-480">Configuration of IIS with web.config</span></span>

<span data-ttu-id="71ae6-481">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="71ae6-481">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="71ae6-482">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="71ae6-482">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="71ae6-483">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="71ae6-483">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="71ae6-484">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="71ae6-484">For more information, see the following topics:</span></span>

* [<span data-ttu-id="71ae6-485">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="71ae6-485">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="71ae6-486">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="71ae6-486">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="71ae6-487">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="71ae6-487">Configuration sections of web.config</span></span>

<span data-ttu-id="71ae6-488">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="71ae6-488">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="71ae6-489">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-489">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="71ae6-490">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-490">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="71ae6-491">应用程序池</span><span class="sxs-lookup"><span data-stu-id="71ae6-491">Application Pools</span></span>

<span data-ttu-id="71ae6-492">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="71ae6-492">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="71ae6-493">托管在进程内：应用需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-493">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="71ae6-494">托管在进程外：建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="71ae6-494">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="71ae6-495">IIS“添加网站”对话框默认为每应用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-495">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="71ae6-496">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-496">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="71ae6-497">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-497">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="71ae6-498">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="71ae6-498">Application Pool Identity</span></span>

<span data-ttu-id="71ae6-499">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="71ae6-499">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="71ae6-500">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="71ae6-500">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="71ae6-501">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="71ae6-501">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="71ae6-503">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="71ae6-503">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="71ae6-504">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="71ae6-504">Resources can be secured using this identity.</span></span> <span data-ttu-id="71ae6-505">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="71ae6-505">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="71ae6-506">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="71ae6-506">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="71ae6-507">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="71ae6-507">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="71ae6-508">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-508">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="71ae6-509">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-509">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="71ae6-510">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="71ae6-510">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="71ae6-511">在“输入要选择的对象名”区域中输入 `IIS AppPool\{APP POOL NAME}`，其中 `{APP POOL NAME}` 占位符为应用池名称。</span><span class="sxs-lookup"><span data-stu-id="71ae6-511">Enter `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="71ae6-512">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="71ae6-512">Select the **Check Names** button.</span></span> <span data-ttu-id="71ae6-513">对于 DefaultAppPool，请使用 `IIS AppPool\DefaultAppPool` 检查名称。</span><span class="sxs-lookup"><span data-stu-id="71ae6-513">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="71ae6-514">如果选择了“检查名称”按钮，对象名称区域中将指示 `DefaultAppPool`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-514">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="71ae6-515">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="71ae6-515">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="71ae6-516">检查对象名称时使用 `IIS AppPool\{APP POOL NAME}` 格式，其中 `{APP POOL NAME}` 占位符是应用池名称。</span><span class="sxs-lookup"><span data-stu-id="71ae6-516">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="71ae6-518">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-518">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="71ae6-520">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-520">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="71ae6-521">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-521">Provide additional permissions as needed.</span></span>

<span data-ttu-id="71ae6-522">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-522">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="71ae6-523">以 `DefaultAppPool` 为例，使用了以下命令：</span><span class="sxs-lookup"><span data-stu-id="71ae6-523">Using the `DefaultAppPool` as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="71ae6-524">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="71ae6-524">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="71ae6-525">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="71ae6-525">HTTP/2 support</span></span>

<span data-ttu-id="71ae6-526">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="71ae6-526">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="71ae6-527">进程内</span><span class="sxs-lookup"><span data-stu-id="71ae6-527">In-process</span></span>
  * <span data-ttu-id="71ae6-528">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-528">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="71ae6-529">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="71ae6-529">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="71ae6-530">进程外</span><span class="sxs-lookup"><span data-stu-id="71ae6-530">Out-of-process</span></span>
  * <span data-ttu-id="71ae6-531">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-531">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="71ae6-532">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="71ae6-532">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="71ae6-533">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="71ae6-533">TLS 1.2 or later connection</span></span>

<span data-ttu-id="71ae6-534">对于建立了 HTTP/2 连接时的进程内部署，[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-534">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="71ae6-535">对于建立了 HTTP/2 连接时的进程外部署，[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-535">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="71ae6-536">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-536">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="71ae6-537">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="71ae6-537">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="71ae6-538">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="71ae6-538">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="71ae6-539">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-539">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="71ae6-540">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="71ae6-540">CORS preflight requests</span></span>

<span data-ttu-id="71ae6-541">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-541">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="71ae6-542">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-542">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="71ae6-543">若要了解如何在 `web.config` 中配置应用的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-543">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="71ae6-544">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="71ae6-544">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="71ae6-545">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-545">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="71ae6-546">[应用程序初始化模块](#application-initialization-module)：可以将托管在[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)的应用配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="71ae6-546">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="71ae6-547">[空闲超时](#idle-timeout)：可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段不超时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-547">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="71ae6-548">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="71ae6-548">Application Initialization Module</span></span>

<span data-ttu-id="71ae6-549">适用于托管在进程内和进程外的应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-549">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="71ae6-550">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-550">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="71ae6-551">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="71ae6-551">The request triggers the app to start.</span></span> <span data-ttu-id="71ae6-552">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-552">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="71ae6-553">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="71ae6-553">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="71ae6-554">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-554">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="71ae6-555">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="71ae6-555">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="71ae6-556">打开“Internet Information Services”>“万维网服务”>“应用程序开发功能”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-556">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="71ae6-557">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="71ae6-557">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="71ae6-558">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="71ae6-558">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="71ae6-559">打开“添加角色和功能向导”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-559">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="71ae6-560">在“选择角色服务”面板中，打开“应用程序开发”节点 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-560">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="71ae6-561">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="71ae6-561">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="71ae6-562">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="71ae6-562">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="71ae6-563">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="71ae6-563">Using IIS Manager:</span></span>

  1. <span data-ttu-id="71ae6-564">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-564">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="71ae6-565">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-565">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="71ae6-566">默认的“启动模式”为“按需” 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-566">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="71ae6-567">将“启动模式”设置为“始终运行”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-567">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="71ae6-568">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-568">Select **OK**.</span></span>
  1. <span data-ttu-id="71ae6-569">打开“连接”面板中的“网站”节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-569">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="71ae6-570">右键单击应用，并选择“管理网站”>“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-570">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="71ae6-571">默认的“预加载已启用”设置为“False” 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-571">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="71ae6-572">将“预加载已启用”设置为“True”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-572">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="71ae6-573">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-573">Select **OK**.</span></span>

* <span data-ttu-id="71ae6-574">使用 `web.config` 向应用的 web.config` 文件中的 `<system.webServer>` 元素添加 `<applicationInitialization>` 元素（其中 `doAppInitAfterRestart` 设置为 `true\`）：</span><span class="sxs-lookup"><span data-stu-id="71ae6-574">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's web.config\` file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="71ae6-575">空闲超时</span><span class="sxs-lookup"><span data-stu-id="71ae6-575">Idle Timeout</span></span>

<span data-ttu-id="71ae6-576">仅适用于托管在进程内的应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-576">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="71ae6-577">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-577">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="71ae6-578">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-578">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="71ae6-579">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-579">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="71ae6-580">默认的“空闲超时(分钟)”为“20”分钟 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-580">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="71ae6-581">将“空闲超时(分钟)”设置为“0”（零） 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-581">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="71ae6-582">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-582">Select **OK**.</span></span>
1. <span data-ttu-id="71ae6-583">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="71ae6-583">Recycle the worker process.</span></span>

<span data-ttu-id="71ae6-584">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="71ae6-584">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="71ae6-585">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="71ae6-585">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="71ae6-586">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-586">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="71ae6-587">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="71ae6-587">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="71ae6-588">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="71ae6-588">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="71ae6-589">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-589">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="71ae6-590">[应用程序池 \<processModel> 的进程模型设置](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-590">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="71ae6-591">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="71ae6-591">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="71ae6-592">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="71ae6-592">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="71ae6-593">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="71ae6-593">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="71ae6-594">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-594">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="71ae6-595">其他资源</span><span class="sxs-lookup"><span data-stu-id="71ae6-595">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="71ae6-596">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="71ae6-596">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="71ae6-597">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="71ae6-597">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="71ae6-598">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="71ae6-598">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="71ae6-599">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-599">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="71ae6-600">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="71ae6-600">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="71ae6-601">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="71ae6-601">Supported operating systems</span></span>

<span data-ttu-id="71ae6-602">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="71ae6-602">The following operating systems are supported:</span></span>

* <span data-ttu-id="71ae6-603">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-603">Windows 7 or later</span></span>
* <span data-ttu-id="71ae6-604">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-604">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="71ae6-605">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-605">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="71ae6-606">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-606">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="71ae6-607">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-607">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="71ae6-608">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-608">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="71ae6-609">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="71ae6-609">Supported platforms</span></span>

<span data-ttu-id="71ae6-610">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-610">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="71ae6-611">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="71ae6-611">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="71ae6-612">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="71ae6-612">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="71ae6-613">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="71ae6-613">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="71ae6-614">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="71ae6-614">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="71ae6-615">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-615">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="71ae6-616">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-616">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="71ae6-617">托管模型</span><span class="sxs-lookup"><span data-stu-id="71ae6-617">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="71ae6-618">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="71ae6-618">In-process hosting model</span></span>

<span data-ttu-id="71ae6-619">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-619">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="71ae6-620">进程内托管的性能高于进程外托管的性能，因为：</span><span class="sxs-lookup"><span data-stu-id="71ae6-620">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="71ae6-621">请求并不通过环回适配器进行代理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-621">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="71ae6-622">环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="71ae6-622">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="71ae6-623">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-623">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="71ae6-624">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="71ae6-624">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="71ae6-625">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="71ae6-625">Performs app initialization.</span></span>
  * <span data-ttu-id="71ae6-626">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-626">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="71ae6-627">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-627">Calls `Program.Main`.</span></span>
* <span data-ttu-id="71ae6-628">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="71ae6-628">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="71ae6-629">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="71ae6-629">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="71ae6-630">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="71ae6-630">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

<span data-ttu-id="71ae6-632">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-632">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="71ae6-633">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-633">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="71ae6-634">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-634">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="71ae6-635">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="71ae6-635">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="71ae6-636">IIS HTTP 服务器处理请求之后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-636">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="71ae6-637">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="71ae6-637">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="71ae6-638">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="71ae6-638">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="71ae6-639">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="71ae6-639">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="71ae6-640">进程内托管选择使用现有应用，但 [dotnet new](/dotnet/core/tools/dotnet-new) 模板默认使用所有 IIS 和 IIS Express 方案的进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="71ae6-640">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="71ae6-641">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。 </span><span class="sxs-lookup"><span data-stu-id="71ae6-641">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="71ae6-642">性能测试表明，与在进程外托管应用并将请求代理到 [Kestrel](xref:fundamentals/servers/kestrel) 服务器相比，在进程中托管 .NET Core 应用可以大大提升请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="71ae6-642">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="71ae6-643">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="71ae6-643">Out-of-process hosting model</span></span>

<span data-ttu-id="71ae6-644">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-644">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="71ae6-645">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-645">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="71ae6-646">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="71ae6-646">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="71ae6-647">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="71ae6-647">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="71ae6-649">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-649">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="71ae6-650">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-650">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="71ae6-651">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="71ae6-651">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="71ae6-652">该模块在启动时通过环境变量指定端口，<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-652">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="71ae6-653">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-653">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="71ae6-654">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="71ae6-654">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="71ae6-655">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-655">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="71ae6-656">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="71ae6-656">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="71ae6-657">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="71ae6-657">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="71ae6-658">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="71ae6-658">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="71ae6-659">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-659">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="71ae6-660">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-660">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="71ae6-661">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="71ae6-661">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="71ae6-662">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="71ae6-662">Enable the IISIntegration components</span></span>

<span data-ttu-id="71ae6-663">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="71ae6-663">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="71ae6-664">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-664">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="71ae6-665">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="71ae6-665">IIS options</span></span>

<span data-ttu-id="71ae6-666">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="71ae6-666">**In-process hosting model**</span></span>

<span data-ttu-id="71ae6-667">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-667">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="71ae6-668">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="71ae6-668">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="71ae6-669">选项</span><span class="sxs-lookup"><span data-stu-id="71ae6-669">Option</span></span>                         | <span data-ttu-id="71ae6-670">默认</span><span class="sxs-lookup"><span data-stu-id="71ae6-670">Default</span></span> | <span data-ttu-id="71ae6-671">设置</span><span class="sxs-lookup"><span data-stu-id="71ae6-671">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="71ae6-672">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-672">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="71ae6-673">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="71ae6-673">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="71ae6-674">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-674">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="71ae6-675">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-675">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="71ae6-676">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="71ae6-676">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="71ae6-677">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="71ae6-677">**Out-of-process hosting model**</span></span>

<span data-ttu-id="71ae6-678">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-678">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="71ae6-679">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="71ae6-679">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="71ae6-680">选项</span><span class="sxs-lookup"><span data-stu-id="71ae6-680">Option</span></span>                         | <span data-ttu-id="71ae6-681">默认</span><span class="sxs-lookup"><span data-stu-id="71ae6-681">Default</span></span> | <span data-ttu-id="71ae6-682">设置</span><span class="sxs-lookup"><span data-stu-id="71ae6-682">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="71ae6-683">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-683">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="71ae6-684">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="71ae6-684">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="71ae6-685">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-685">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="71ae6-686">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="71ae6-686">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="71ae6-687">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="71ae6-687">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="71ae6-688">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-688">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="71ae6-689">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="71ae6-689">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="71ae6-690">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="71ae6-690">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="71ae6-691">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-691">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="71ae6-692">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-692">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="71ae6-693">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="71ae6-693">web.config file</span></span>

<span data-ttu-id="71ae6-694">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-694">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="71ae6-695">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-695">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="71ae6-696">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-696">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="71ae6-697">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="71ae6-697">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="71ae6-698">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-698">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="71ae6-699">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="71ae6-699">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="71ae6-700">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-700">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="71ae6-701">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="71ae6-701">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="71ae6-702">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="71ae6-702">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="71ae6-703">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="71ae6-703">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="71ae6-704">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="71ae6-704">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="71ae6-705">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-705">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="71ae6-706">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="71ae6-706">web.config file location</span></span>

<span data-ttu-id="71ae6-707">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config 文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-707">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="71ae6-708">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="71ae6-708">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="71ae6-709">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-709">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="71ae6-710">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-710">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="71ae6-711">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="71ae6-711">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="71ae6-712">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-712">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="71ae6-713">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="71ae6-713">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="71ae6-714">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="71ae6-714">Transform web.config</span></span>

<span data-ttu-id="71ae6-715">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-715">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="71ae6-716">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="71ae6-716">IIS configuration</span></span>

<span data-ttu-id="71ae6-717">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="71ae6-717">**Windows Server operating systems**</span></span>

<span data-ttu-id="71ae6-718">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="71ae6-718">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="71ae6-719">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="71ae6-719">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="71ae6-720">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-720">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="71ae6-722">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="71ae6-722">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="71ae6-723">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="71ae6-723">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="71ae6-725">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-725">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="71ae6-726">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-726">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="71ae6-727">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-727">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="71ae6-728">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-728">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="71ae6-729">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-729">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="71ae6-730">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-730">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="71ae6-731">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-731">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="71ae6-732">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-732">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="71ae6-733">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-733">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="71ae6-734">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="71ae6-734">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="71ae6-735">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="71ae6-735">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="71ae6-736">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="71ae6-736">**Windows desktop operating systems**</span></span>

<span data-ttu-id="71ae6-737">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-737">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="71ae6-738">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="71ae6-738">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="71ae6-739">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-739">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="71ae6-740">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-740">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="71ae6-741">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="71ae6-741">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="71ae6-742">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="71ae6-742">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="71ae6-743">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-743">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="71ae6-744">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-744">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="71ae6-745">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-745">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="71ae6-746">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-746">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="71ae6-747">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-747">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="71ae6-748">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-748">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="71ae6-749">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-749">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="71ae6-750">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-750">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="71ae6-751">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-751">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="71ae6-752">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-752">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="71ae6-753">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="71ae6-753">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="71ae6-755">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="71ae6-755">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="71ae6-756">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="71ae6-756">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="71ae6-757">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-757">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="71ae6-758">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-758">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71ae6-759">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="71ae6-759">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="71ae6-760">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-760">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="71ae6-761">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-761">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="71ae6-762">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-762">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="71ae6-763">下载</span><span class="sxs-lookup"><span data-stu-id="71ae6-763">Download</span></span>

1. <span data-ttu-id="71ae6-764">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="71ae6-764">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="71ae6-765">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-765">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="71ae6-766">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-766">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="71ae6-767">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-767">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="71ae6-768">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-768">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="71ae6-769">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-769">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="71ae6-770">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="71ae6-770">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="71ae6-771">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-771">Run the installer on the server.</span></span> <span data-ttu-id="71ae6-772">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="71ae6-772">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="71ae6-773">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="71ae6-773">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="71ae6-774">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-774">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="71ae6-775">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-775">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="71ae6-776">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-776">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="71ae6-777">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-777">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="71ae6-778">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-778">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="71ae6-779">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="71ae6-779">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="71ae6-780">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-780">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="71ae6-781">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (applicationHost.config) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="71ae6-781">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="71ae6-782">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-782">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="71ae6-783">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-783">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="71ae6-784">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="71ae6-784">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="71ae6-785">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="71ae6-785">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="71ae6-786">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-786">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="71ae6-787">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="71ae6-787">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="71ae6-788">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="71ae6-788">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="71ae6-789">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="71ae6-789">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="71ae6-790">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="71ae6-790">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="71ae6-791">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="71ae6-791">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="71ae6-792">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-792">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="71ae6-793">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-793">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="71ae6-794">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="71ae6-794">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="71ae6-795">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-795">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="71ae6-796">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="71ae6-796">The preferred method is to use WebPI.</span></span> <span data-ttu-id="71ae6-797">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-797">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="71ae6-798">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="71ae6-798">Create the IIS site</span></span>

1. <span data-ttu-id="71ae6-799">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-799">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="71ae6-800">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="71ae6-800">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="71ae6-801">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-801">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="71ae6-802">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-802">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="71ae6-803">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="71ae6-803">Right-click the **Sites** folder.</span></span> <span data-ttu-id="71ae6-804">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-804">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="71ae6-805">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-805">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="71ae6-806">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="71ae6-806">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="71ae6-808">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-808">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="71ae6-809">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="71ae6-809">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="71ae6-810">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="71ae6-810">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="71ae6-811">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="71ae6-811">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="71ae6-812">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="71ae6-812">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="71ae6-813">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-813">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="71ae6-814">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-814">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="71ae6-815">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-815">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="71ae6-816">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="71ae6-816">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="71ae6-818">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-818">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="71ae6-819">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-819">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="71ae6-820">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-820">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="71ae6-821">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-821">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="71ae6-822">在 IIS 管理器 >“应用程序池”的“操作”侧栏中，选择“设置应用程序池默认设置”或“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-822">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="71ae6-823">找到“启用 32 位应用程序”并将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-823">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="71ae6-824">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-824">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="71ae6-825">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-825">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="71ae6-826">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-826">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="71ae6-827">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-827">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="71ae6-828">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-828">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="71ae6-829">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-829">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="71ae6-830">部署应用</span><span class="sxs-lookup"><span data-stu-id="71ae6-830">Deploy the app</span></span>

<span data-ttu-id="71ae6-831">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="71ae6-831">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="71ae6-832">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="71ae6-832">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="71ae6-833">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-833">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="71ae6-834">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-834">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="71ae6-835">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="71ae6-835">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="71ae6-837">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-837">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="71ae6-838">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-838">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="71ae6-839">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-839">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="71ae6-840">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="71ae6-840">Alternatives to Web Deploy</span></span>

<span data-ttu-id="71ae6-841">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="71ae6-841">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="71ae6-842">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="71ae6-842">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="71ae6-843">浏览网站</span><span class="sxs-lookup"><span data-stu-id="71ae6-843">Browse the website</span></span>

<span data-ttu-id="71ae6-844">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-844">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="71ae6-845">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-845">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="71ae6-846">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="71ae6-846">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="71ae6-848">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="71ae6-848">Locked deployment files</span></span>

<span data-ttu-id="71ae6-849">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="71ae6-849">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="71ae6-850">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-850">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="71ae6-851">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="71ae6-851">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="71ae6-852">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-852">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="71ae6-853">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-853">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="71ae6-854">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-854">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="71ae6-855">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-855">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="71ae6-856">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-856">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="71ae6-857">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="71ae6-857">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="71ae6-858">数据保护</span><span class="sxs-lookup"><span data-stu-id="71ae6-858">Data protection</span></span>

<span data-ttu-id="71ae6-859">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-859">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="71ae6-860">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-860">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="71ae6-861">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="71ae6-861">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="71ae6-862">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-862">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="71ae6-863">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="71ae6-863">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="71ae6-864">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="71ae6-864">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="71ae6-865">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="71ae6-865">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="71ae6-866">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-866">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="71ae6-867">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="71ae6-867">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="71ae6-868">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="71ae6-868">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="71ae6-869">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-869">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="71ae6-870">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="71ae6-870">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="71ae6-871">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-871">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="71ae6-872">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="71ae6-872">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="71ae6-873">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="71ae6-873">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="71ae6-874">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="71ae6-874">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="71ae6-875">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="71ae6-875">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="71ae6-876">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="71ae6-876">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="71ae6-877">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="71ae6-877">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="71ae6-878">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-878">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="71ae6-879">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-879">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="71ae6-880">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="71ae6-880">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="71ae6-881">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-881">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="71ae6-882">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-882">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="71ae6-883">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="71ae6-883">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="71ae6-884">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-884">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="71ae6-885">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-885">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="71ae6-886">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-886">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="71ae6-887">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-887">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="71ae6-888">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="71ae6-888">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="71ae6-889">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="71ae6-889">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="71ae6-890">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-890">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="71ae6-891">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="71ae6-891">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="71ae6-892">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-892">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="71ae6-893">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="71ae6-893">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="71ae6-894">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-894">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="71ae6-895">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="71ae6-895">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="71ae6-896">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-896">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="71ae6-897">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-897">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="71ae6-898">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="71ae6-898">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="71ae6-899">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="71ae6-899">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="71ae6-900">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-900">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="71ae6-901">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="71ae6-901">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="71ae6-902">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-902">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="71ae6-903">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-903">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="71ae6-904">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="71ae6-904">Virtual Directories</span></span>

<span data-ttu-id="71ae6-905">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-905">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="71ae6-906">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-906">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="71ae6-907">子应用程序</span><span class="sxs-lookup"><span data-stu-id="71ae6-907">Sub-applications</span></span>

<span data-ttu-id="71ae6-908">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-908">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="71ae6-909">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="71ae6-909">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="71ae6-910">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="71ae6-910">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="71ae6-911">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="71ae6-911">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="71ae6-912">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-912">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="71ae6-913">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-913">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="71ae6-914">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-914">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="71ae6-915">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="71ae6-915">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="71ae6-916">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="71ae6-916">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="71ae6-917">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="71ae6-917">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="71ae6-918">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-918">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="71ae6-919">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-919">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="71ae6-920">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-920">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="71ae6-921">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-921">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="71ae6-922">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="71ae6-922">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="71ae6-923">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-923">Select **OK**.</span></span>

<span data-ttu-id="71ae6-924">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-924">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="71ae6-925">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-925">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="71ae6-926">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="71ae6-926">Configuration of IIS with web.config</span></span>

<span data-ttu-id="71ae6-927">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="71ae6-927">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="71ae6-928">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="71ae6-928">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="71ae6-929">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="71ae6-929">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="71ae6-930">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="71ae6-930">For more information, see the following topics:</span></span>

* [<span data-ttu-id="71ae6-931">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="71ae6-931">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="71ae6-932">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="71ae6-932">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="71ae6-933">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="71ae6-933">Configuration sections of web.config</span></span>

<span data-ttu-id="71ae6-934">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="71ae6-934">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="71ae6-935">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-935">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="71ae6-936">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-936">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="71ae6-937">应用程序池</span><span class="sxs-lookup"><span data-stu-id="71ae6-937">Application Pools</span></span>

<span data-ttu-id="71ae6-938">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="71ae6-938">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="71ae6-939">托管在进程内：应用需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-939">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="71ae6-940">托管在进程外：建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="71ae6-940">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="71ae6-941">IIS“添加网站”对话框默认为每应用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-941">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="71ae6-942">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-942">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="71ae6-943">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-943">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="71ae6-944">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="71ae6-944">Application Pool Identity</span></span>

<span data-ttu-id="71ae6-945">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="71ae6-945">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="71ae6-946">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="71ae6-946">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="71ae6-947">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="71ae6-947">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="71ae6-949">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="71ae6-949">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="71ae6-950">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="71ae6-950">Resources can be secured using this identity.</span></span> <span data-ttu-id="71ae6-951">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="71ae6-951">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="71ae6-952">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="71ae6-952">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="71ae6-953">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="71ae6-953">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="71ae6-954">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-954">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="71ae6-955">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-955">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="71ae6-956">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="71ae6-956">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="71ae6-957">在“输入要选择的对象名称”区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-957">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="71ae6-958">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="71ae6-958">Select the **Check Names** button.</span></span> <span data-ttu-id="71ae6-959">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="71ae6-959">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="71ae6-960">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="71ae6-960">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="71ae6-961">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="71ae6-961">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="71ae6-962">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="71ae6-962">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="71ae6-964">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-964">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="71ae6-966">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-966">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="71ae6-967">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-967">Provide additional permissions as needed.</span></span>

<span data-ttu-id="71ae6-968">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-968">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="71ae6-969">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="71ae6-969">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="71ae6-970">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="71ae6-970">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="71ae6-971">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="71ae6-971">HTTP/2 support</span></span>

<span data-ttu-id="71ae6-972">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="71ae6-972">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="71ae6-973">进程内</span><span class="sxs-lookup"><span data-stu-id="71ae6-973">In-process</span></span>
  * <span data-ttu-id="71ae6-974">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-974">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="71ae6-975">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="71ae6-975">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="71ae6-976">进程外</span><span class="sxs-lookup"><span data-stu-id="71ae6-976">Out-of-process</span></span>
  * <span data-ttu-id="71ae6-977">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-977">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="71ae6-978">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="71ae6-978">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="71ae6-979">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="71ae6-979">TLS 1.2 or later connection</span></span>

<span data-ttu-id="71ae6-980">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-980">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="71ae6-981">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-981">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="71ae6-982">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-982">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="71ae6-983">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="71ae6-983">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="71ae6-984">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="71ae6-984">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="71ae6-985">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-985">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="71ae6-986">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="71ae6-986">CORS preflight requests</span></span>

<span data-ttu-id="71ae6-987">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-987">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="71ae6-988">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-988">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="71ae6-989">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-989">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="71ae6-990">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="71ae6-990">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="71ae6-991">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-991">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="71ae6-992">[应用程序初始化模块](#application-initialization-module)：可以将托管在[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)的应用配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="71ae6-992">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="71ae6-993">[空闲超时](#idle-timeout)：可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段不超时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-993">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="71ae6-994">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="71ae6-994">Application Initialization Module</span></span>

<span data-ttu-id="71ae6-995">适用于托管在进程内和进程外的应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-995">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="71ae6-996">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-996">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="71ae6-997">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="71ae6-997">The request triggers the app to start.</span></span> <span data-ttu-id="71ae6-998">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-998">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="71ae6-999">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="71ae6-999">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="71ae6-1000">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1000">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="71ae6-1001">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1001">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="71ae6-1002">打开“Internet Information Services”>“万维网服务”>“应用程序开发功能”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1002">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="71ae6-1003">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1003">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="71ae6-1004">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1004">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="71ae6-1005">打开“添加角色和功能向导”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1005">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="71ae6-1006">在“选择角色服务”面板中，打开“应用程序开发”节点 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1006">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="71ae6-1007">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1007">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="71ae6-1008">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1008">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="71ae6-1009">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1009">Using IIS Manager:</span></span>

  1. <span data-ttu-id="71ae6-1010">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1010">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="71ae6-1011">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1011">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="71ae6-1012">默认的“启动模式”为“按需” 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1012">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="71ae6-1013">将“启动模式”设置为“始终运行”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1013">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="71ae6-1014">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1014">Select **OK**.</span></span>
  1. <span data-ttu-id="71ae6-1015">打开“连接”面板中的“网站”节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1015">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="71ae6-1016">右键单击应用，并选择“管理网站”>“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1016">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="71ae6-1017">默认的“预加载已启用”设置为“False” 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1017">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="71ae6-1018">将“预加载已启用”设置为“True”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1018">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="71ae6-1019">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1019">Select **OK**.</span></span>

* <span data-ttu-id="71ae6-1020">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中 ：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1020">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="71ae6-1021">空闲超时</span><span class="sxs-lookup"><span data-stu-id="71ae6-1021">Idle Timeout</span></span>

<span data-ttu-id="71ae6-1022">仅适用于托管在进程内的应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1022">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="71ae6-1023">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1023">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="71ae6-1024">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1024">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="71ae6-1025">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1025">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="71ae6-1026">默认的“空闲超时(分钟)”为“20”分钟 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1026">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="71ae6-1027">将“空闲超时(分钟)”设置为“0”（零） 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1027">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="71ae6-1028">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1028">Select **OK**.</span></span>
1. <span data-ttu-id="71ae6-1029">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1029">Recycle the worker process.</span></span>

<span data-ttu-id="71ae6-1030">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1030">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="71ae6-1031">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1031">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="71ae6-1032">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1032">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="71ae6-1033">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="71ae6-1033">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="71ae6-1034">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="71ae6-1034">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="71ae6-1035">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1035">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="71ae6-1036">[应用程序池 \<processModel> 的进程模型设置](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1036">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="71ae6-1037">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="71ae6-1037">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="71ae6-1038">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="71ae6-1038">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="71ae6-1039">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="71ae6-1039">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="71ae6-1040">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-1040">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="71ae6-1041">其他资源</span><span class="sxs-lookup"><span data-stu-id="71ae6-1041">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="71ae6-1042">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="71ae6-1042">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="71ae6-1043">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="71ae6-1043">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="71ae6-1044">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="71ae6-1044">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="71ae6-1045">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1045">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="71ae6-1046">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="71ae6-1046">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="71ae6-1047">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="71ae6-1047">Supported operating systems</span></span>

<span data-ttu-id="71ae6-1048">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1048">The following operating systems are supported:</span></span>

* <span data-ttu-id="71ae6-1049">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-1049">Windows 7 or later</span></span>
* <span data-ttu-id="71ae6-1050">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-1050">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="71ae6-1051">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1051">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="71ae6-1052">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1052">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="71ae6-1053">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1053">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="71ae6-1054">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1054">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="71ae6-1055">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="71ae6-1055">Supported platforms</span></span>

<span data-ttu-id="71ae6-1056">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1056">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="71ae6-1057">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1057">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="71ae6-1058">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1058">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="71ae6-1059">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1059">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="71ae6-1060">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1060">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="71ae6-1061">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1061">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="71ae6-1062">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1062">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="71ae6-1063">ASP.NET Core 随附有 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，后者是默认的跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1063">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="71ae6-1064">使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用在独立于 IIS 工作进程（进程外）和 [Kestrel 服务器](xref:fundamentals/servers/index#kestrel)的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1064">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="71ae6-1065">由于 ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，因此该模块会处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1065">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="71ae6-1066">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1066">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="71ae6-1067">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1067">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="71ae6-1068">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1068">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="71ae6-1070">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1070">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="71ae6-1071">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1071">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="71ae6-1072">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1072">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="71ae6-1073">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1073">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="71ae6-1074">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1074">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="71ae6-1075">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1075">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="71ae6-1076">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1076">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="71ae6-1077">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1077">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="71ae6-1078">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1078">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="71ae6-1079">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1079">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="71ae6-1080">`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的基础路径和端口来启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1080">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="71ae6-1081">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1081">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="71ae6-1082">`CreateDefaultBuilder` 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1082">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> method.</span></span> <span data-ttu-id="71ae6-1083">`UseIISIntegration` 配置在 localhost IP 地址 (`127.0.0.1`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1083">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="71ae6-1084">如果动态端口为 1234，则 Kestrel 在 `127.0.0.1:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1084">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="71ae6-1085">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1085">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="71ae6-1086">Kestrel 的侦听 API</span><span class="sxs-lookup"><span data-stu-id="71ae6-1086">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="71ae6-1087">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="71ae6-1087">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="71ae6-1088">使用模块时，不需要调用 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1088">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="71ae6-1089">如果调用 `UseUrls` 或 `Listen`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1089">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="71ae6-1090">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1090">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="71ae6-1091">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1091">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="71ae6-1092">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="71ae6-1092">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="71ae6-1093">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="71ae6-1093">Enable the IISIntegration components</span></span>

<span data-ttu-id="71ae6-1094">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1094">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="71ae6-1095">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1095">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="71ae6-1096">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="71ae6-1096">IIS options</span></span>

| <span data-ttu-id="71ae6-1097">选项</span><span class="sxs-lookup"><span data-stu-id="71ae6-1097">Option</span></span>                         | <span data-ttu-id="71ae6-1098">默认</span><span class="sxs-lookup"><span data-stu-id="71ae6-1098">Default</span></span> | <span data-ttu-id="71ae6-1099">设置</span><span class="sxs-lookup"><span data-stu-id="71ae6-1099">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="71ae6-1100">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1100">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="71ae6-1101">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1101">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="71ae6-1102">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1102">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="71ae6-1103">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1103">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="71ae6-1104">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1104">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="71ae6-1105">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1105">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="71ae6-1106">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1106">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="71ae6-1107">选项</span><span class="sxs-lookup"><span data-stu-id="71ae6-1107">Option</span></span>                         | <span data-ttu-id="71ae6-1108">默认</span><span class="sxs-lookup"><span data-stu-id="71ae6-1108">Default</span></span> | <span data-ttu-id="71ae6-1109">设置</span><span class="sxs-lookup"><span data-stu-id="71ae6-1109">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="71ae6-1110">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1110">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="71ae6-1111">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1111">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="71ae6-1112">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1112">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="71ae6-1113">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1113">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="71ae6-1114">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1114">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="71ae6-1115">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1115">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="71ae6-1116">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="71ae6-1116">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="71ae6-1117">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1117">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="71ae6-1118">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1118">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="71ae6-1119">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1119">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="71ae6-1120">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="71ae6-1120">web.config file</span></span>

<span data-ttu-id="71ae6-1121">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1121">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="71ae6-1122">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1122">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="71ae6-1123">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1123">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="71ae6-1124">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1124">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="71ae6-1125">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1125">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="71ae6-1126">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1126">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="71ae6-1127">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1127">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="71ae6-1128">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1128">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="71ae6-1129">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1129">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="71ae6-1130">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1130">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="71ae6-1131">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1131">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="71ae6-1132">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1132">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="71ae6-1133">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="71ae6-1133">web.config file location</span></span>

<span data-ttu-id="71ae6-1134">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config 文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1134">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="71ae6-1135">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1135">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="71ae6-1136">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1136">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="71ae6-1137">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1137">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="71ae6-1138">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1138">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="71ae6-1139">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1139">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="71ae6-1140">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1140">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="71ae6-1141">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="71ae6-1141">Transform web.config</span></span>

<span data-ttu-id="71ae6-1142">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1142">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="71ae6-1143">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="71ae6-1143">IIS configuration</span></span>

<span data-ttu-id="71ae6-1144">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1144">**Windows Server operating systems**</span></span>

<span data-ttu-id="71ae6-1145">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1145">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="71ae6-1146">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1146">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="71ae6-1147">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1147">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="71ae6-1149">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1149">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="71ae6-1150">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1150">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="71ae6-1152">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1152">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="71ae6-1153">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1153">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="71ae6-1154">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1154">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="71ae6-1155">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1155">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="71ae6-1156">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1156">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="71ae6-1157">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1157">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="71ae6-1158">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1158">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="71ae6-1159">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1159">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="71ae6-1160">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1160">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="71ae6-1161">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1161">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="71ae6-1162">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1162">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="71ae6-1163">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1163">**Windows desktop operating systems**</span></span>

<span data-ttu-id="71ae6-1164">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1164">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="71ae6-1165">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1165">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="71ae6-1166">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1166">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="71ae6-1167">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1167">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="71ae6-1168">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1168">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="71ae6-1169">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1169">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="71ae6-1170">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1170">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="71ae6-1171">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1171">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="71ae6-1172">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1172">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="71ae6-1173">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1173">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="71ae6-1174">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1174">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="71ae6-1175">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1175">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="71ae6-1176">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1176">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="71ae6-1177">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1177">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="71ae6-1178">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1178">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="71ae6-1179">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1179">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="71ae6-1180">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1180">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="71ae6-1182">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="71ae6-1182">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="71ae6-1183">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1183">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="71ae6-1184">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1184">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="71ae6-1185">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1185">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="71ae6-1186">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1186">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="71ae6-1187">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1187">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="71ae6-1188">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1188">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="71ae6-1189">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1189">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="71ae6-1190">下载</span><span class="sxs-lookup"><span data-stu-id="71ae6-1190">Download</span></span>

1. <span data-ttu-id="71ae6-1191">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1191">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="71ae6-1192">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1192">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="71ae6-1193">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1193">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="71ae6-1194">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1194">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="71ae6-1195">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1195">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="71ae6-1196">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1196">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="71ae6-1197">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="71ae6-1197">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="71ae6-1198">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1198">Run the installer on the server.</span></span> <span data-ttu-id="71ae6-1199">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1199">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="71ae6-1200">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1200">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="71ae6-1201">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1201">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="71ae6-1202">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1202">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="71ae6-1203">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1203">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="71ae6-1204">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1204">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="71ae6-1205">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1205">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="71ae6-1206">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1206">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="71ae6-1207">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1207">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="71ae6-1208">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (applicationHost.config) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1208">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="71ae6-1209">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1209">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="71ae6-1210">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1210">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="71ae6-1211">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1211">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="71ae6-1212">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1212">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="71ae6-1213">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1213">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="71ae6-1214">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1214">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="71ae6-1215">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1215">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="71ae6-1216">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1216">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="71ae6-1217">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1217">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="71ae6-1218">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1218">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="71ae6-1219">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1219">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="71ae6-1220">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-1220">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="71ae6-1221">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1221">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="71ae6-1222">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1222">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="71ae6-1223">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1223">The preferred method is to use WebPI.</span></span> <span data-ttu-id="71ae6-1224">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1224">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="71ae6-1225">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="71ae6-1225">Create the IIS site</span></span>

1. <span data-ttu-id="71ae6-1226">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1226">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="71ae6-1227">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1227">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="71ae6-1228">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1228">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="71ae6-1229">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1229">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="71ae6-1230">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1230">Right-click the **Sites** folder.</span></span> <span data-ttu-id="71ae6-1231">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1231">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="71ae6-1232">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1232">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="71ae6-1233">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1233">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="71ae6-1235">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1235">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="71ae6-1236">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1236">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="71ae6-1237">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1237">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="71ae6-1238">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1238">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="71ae6-1239">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1239">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="71ae6-1240">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1240">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="71ae6-1241">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1241">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="71ae6-1242">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1242">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="71ae6-1243">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1243">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="71ae6-1245">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1245">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="71ae6-1246">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1246">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="71ae6-1247">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1247">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="71ae6-1248">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1248">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="71ae6-1249">在 IIS 管理器 >“应用程序池”的“操作”侧栏中，选择“设置应用程序池默认设置”或“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1249">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="71ae6-1250">找到“启用 32 位应用程序”并将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1250">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="71ae6-1251">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1251">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="71ae6-1252">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1252">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="71ae6-1253">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1253">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="71ae6-1254">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1254">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="71ae6-1255">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1255">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="71ae6-1256">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1256">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="71ae6-1257">部署应用</span><span class="sxs-lookup"><span data-stu-id="71ae6-1257">Deploy the app</span></span>

<span data-ttu-id="71ae6-1258">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1258">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="71ae6-1259">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1259">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="71ae6-1260">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-1260">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="71ae6-1261">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1261">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="71ae6-1262">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1262">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="71ae6-1264">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-1264">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="71ae6-1265">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1265">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="71ae6-1266">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1266">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="71ae6-1267">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="71ae6-1267">Alternatives to Web Deploy</span></span>

<span data-ttu-id="71ae6-1268">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1268">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="71ae6-1269">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1269">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="71ae6-1270">浏览网站</span><span class="sxs-lookup"><span data-stu-id="71ae6-1270">Browse the website</span></span>

<span data-ttu-id="71ae6-1271">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1271">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="71ae6-1272">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1272">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="71ae6-1273">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1273">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="71ae6-1275">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="71ae6-1275">Locked deployment files</span></span>

<span data-ttu-id="71ae6-1276">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1276">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="71ae6-1277">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1277">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="71ae6-1278">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1278">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="71ae6-1279">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1279">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="71ae6-1280">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1280">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="71ae6-1281">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1281">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="71ae6-1282">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1282">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="71ae6-1283">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1283">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="71ae6-1284">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1284">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="71ae6-1285">数据保护</span><span class="sxs-lookup"><span data-stu-id="71ae6-1285">Data protection</span></span>

<span data-ttu-id="71ae6-1286">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1286">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="71ae6-1287">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1287">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="71ae6-1288">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1288">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="71ae6-1289">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1289">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="71ae6-1290">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1290">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="71ae6-1291">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1291">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="71ae6-1292">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1292">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="71ae6-1293">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1293">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="71ae6-1294">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1294">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="71ae6-1295">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1295">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="71ae6-1296">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1296">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="71ae6-1297">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1297">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="71ae6-1298">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1298">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="71ae6-1299">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1299">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="71ae6-1300">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1300">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="71ae6-1301">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1301">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="71ae6-1302">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1302">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="71ae6-1303">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1303">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="71ae6-1304">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1304">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="71ae6-1305">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1305">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="71ae6-1306">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1306">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="71ae6-1307">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1307">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="71ae6-1308">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1308">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="71ae6-1309">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1309">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="71ae6-1310">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1310">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="71ae6-1311">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1311">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="71ae6-1312">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1312">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="71ae6-1313">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1313">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="71ae6-1314">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1314">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="71ae6-1315">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1315">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="71ae6-1316">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1316">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="71ae6-1317">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1317">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="71ae6-1318">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1318">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="71ae6-1319">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1319">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="71ae6-1320">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1320">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="71ae6-1321">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1321">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="71ae6-1322">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1322">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="71ae6-1323">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1323">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="71ae6-1324">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1324">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="71ae6-1325">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1325">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="71ae6-1326">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1326">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="71ae6-1327">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1327">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="71ae6-1328">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="71ae6-1328">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="71ae6-1329">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1329">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="71ae6-1330">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1330">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="71ae6-1331">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="71ae6-1331">Virtual Directories</span></span>

<span data-ttu-id="71ae6-1332">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1332">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="71ae6-1333">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1333">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="71ae6-1334">子应用程序</span><span class="sxs-lookup"><span data-stu-id="71ae6-1334">Sub-applications</span></span>

<span data-ttu-id="71ae6-1335">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1335">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="71ae6-1336">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1336">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="71ae6-1337">子应用不应将 ASP.NET Core 模块作为处理程序包含在其中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1337">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="71ae6-1338">如果在子应用的 web.config 文件中将该模块添加为处理程序，则在尝试浏览子应用时会收到“500.19 内部服务器错误”，即引用错误的配置文件。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1338">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="71ae6-1339">以下示例显示 ASP.NET Core 子应用的已发布 web.config 文件：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1339">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="71ae6-1340">在 ASP.NET Core 应用下托管非 ASP.NET Core 子应用时，需在子应用的 web.config 文件中显式删除继承的处理程序：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1340">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

<span data-ttu-id="71ae6-1341">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1341">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="71ae6-1342">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1342">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="71ae6-1343">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1343">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="71ae6-1344">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1344">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="71ae6-1345">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1345">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="71ae6-1346">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1346">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="71ae6-1347">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1347">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="71ae6-1348">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1348">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="71ae6-1349">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1349">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="71ae6-1350">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1350">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="71ae6-1351">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1351">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="71ae6-1352">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1352">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="71ae6-1353">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="71ae6-1353">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="71ae6-1354">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1354">Select **OK**.</span></span>

<span data-ttu-id="71ae6-1355">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1355">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="71ae6-1356">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1356">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="71ae6-1357">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="71ae6-1357">Configuration of IIS with web.config</span></span>

<span data-ttu-id="71ae6-1358">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1358">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="71ae6-1359">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1359">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="71ae6-1360">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1360">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="71ae6-1361">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1361">For more information, see the following topics:</span></span>

* [<span data-ttu-id="71ae6-1362">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="71ae6-1362">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="71ae6-1363">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1363">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="71ae6-1364">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="71ae6-1364">Configuration sections of web.config</span></span>

<span data-ttu-id="71ae6-1365">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1365">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="71ae6-1366">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1366">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="71ae6-1367">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1367">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="71ae6-1368">应用程序池</span><span class="sxs-lookup"><span data-stu-id="71ae6-1368">Application Pools</span></span>

<span data-ttu-id="71ae6-1369">在服务器上托管多个网站时，建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1369">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="71ae6-1370">IIS“添加网站”对话框默认执行此配置。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1370">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="71ae6-1371">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1371">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="71ae6-1372">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1372">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="71ae6-1373">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="71ae6-1373">Application Pool Identity</span></span>

<span data-ttu-id="71ae6-1374">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1374">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="71ae6-1375">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1375">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="71ae6-1376">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1376">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="71ae6-1378">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1378">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="71ae6-1379">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1379">Resources can be secured using this identity.</span></span> <span data-ttu-id="71ae6-1380">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1380">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="71ae6-1381">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1381">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="71ae6-1382">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1382">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="71ae6-1383">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1383">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="71ae6-1384">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1384">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="71ae6-1385">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1385">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="71ae6-1386">在“输入要选择的对象名称”区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1386">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="71ae6-1387">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1387">Select the **Check Names** button.</span></span> <span data-ttu-id="71ae6-1388">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1388">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="71ae6-1389">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1389">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="71ae6-1390">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1390">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="71ae6-1391">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1391">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="71ae6-1393">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1393">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="71ae6-1395">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1395">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="71ae6-1396">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1396">Provide additional permissions as needed.</span></span>

<span data-ttu-id="71ae6-1397">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1397">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="71ae6-1398">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1398">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="71ae6-1399">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1399">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="71ae6-1400">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="71ae6-1400">HTTP/2 support</span></span>

<span data-ttu-id="71ae6-1401">满足以下基本要求的进程外部署支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="71ae6-1401">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="71ae6-1402">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71ae6-1402">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="71ae6-1403">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1403">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="71ae6-1404">目标框架：不适用于进程外部署，因为 HTTP/2 连接完全由 IIS 处理。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1404">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="71ae6-1405">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="71ae6-1405">TLS 1.2 or later connection</span></span>

<span data-ttu-id="71ae6-1406">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1406">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="71ae6-1407">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1407">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="71ae6-1408">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1408">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="71ae6-1409">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1409">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="71ae6-1410">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="71ae6-1410">CORS preflight requests</span></span>

<span data-ttu-id="71ae6-1411">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1411">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="71ae6-1412">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1412">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="71ae6-1413">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="71ae6-1413">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="71ae6-1414">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="71ae6-1414">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="71ae6-1415">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="71ae6-1415">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="71ae6-1416">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="71ae6-1416">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="71ae6-1417">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="71ae6-1417">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="71ae6-1418">其他资源</span><span class="sxs-lookup"><span data-stu-id="71ae6-1418">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="71ae6-1419">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="71ae6-1419">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="71ae6-1420">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="71ae6-1420">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="71ae6-1421">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="71ae6-1421">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
