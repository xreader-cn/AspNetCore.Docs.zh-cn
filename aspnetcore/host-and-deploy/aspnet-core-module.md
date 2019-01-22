---
title: ASP.NET Core 模块
author: guardrex
description: 了解如何配置 ASP.NET Core 模块以托管 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 192e4bf8e970083cc05babcd7fb3cf52985e35bf
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396319"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="749bb-103">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="749bb-103">ASP.NET Core Module</span></span>

<span data-ttu-id="749bb-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="749bb-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="749bb-105">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="749bb-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="749bb-106">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="749bb-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="749bb-107">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="749bb-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="749bb-108">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="749bb-108">Supported Windows versions:</span></span>

* <span data-ttu-id="749bb-109">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="749bb-109">Windows 7 or later</span></span>
* <span data-ttu-id="749bb-110">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="749bb-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="749bb-111">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="749bb-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="749bb-112">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="749bb-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="749bb-113">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="749bb-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="749bb-114">托管模型</span><span class="sxs-lookup"><span data-stu-id="749bb-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="749bb-115">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="749bb-115">In-process hosting model</span></span>

<span data-ttu-id="749bb-116">若要配置用于进程内托管的应用，请将 `<AspNetCoreHostingModel>` 属性添加到值为 `InProcess`（进程外托管使用 `OutOfProcess` 进行设置）的应用项目文件：</span><span class="sxs-lookup"><span data-stu-id="749bb-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="749bb-117">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="749bb-117">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="749bb-118">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="749bb-118">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="749bb-119">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="749bb-119">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="749bb-120">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="749bb-120">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

* <span data-ttu-id="749bb-121">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="749bb-121">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="749bb-122">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="749bb-122">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="749bb-123">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="749bb-123">Use one app pool per app.</span></span>

* <span data-ttu-id="749bb-124">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="749bb-124">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="749bb-125">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="749bb-125">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="749bb-126">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="749bb-126">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="749bb-127">如果使用 `WebHostBuilder`（而不是使用 [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)）手动设置应用的主机，并且应用曾经直接在 Kestrel 服务器上运行（自托管），则先调用 `UseKestrel`，再调用 `UseIISIntegration`。</span><span class="sxs-lookup"><span data-stu-id="749bb-127">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="749bb-128">如果顺序颠倒，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="749bb-128">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="749bb-129">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="749bb-129">Client disconnects are detected.</span></span> <span data-ttu-id="749bb-130">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="749bb-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="749bb-131"><xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv）。</span><span class="sxs-lookup"><span data-stu-id="749bb-131"><xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="749bb-132">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="749bb-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="749bb-133">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="749bb-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="749bb-134">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="749bb-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="749bb-135">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="749bb-135">Out-of-process hosting model</span></span>

<span data-ttu-id="749bb-136">若要配置进程外托管应用，请在项目文件中使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="749bb-136">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="749bb-137">不指定 `<AspNetCoreHostingModel>` 属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-137">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="749bb-138">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="749bb-138">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="749bb-139">将 `<AspNetCoreHostingModel>` 属性的值设为 `OutOfProcess`（进程内托管设为 `InProcess`）：</span><span class="sxs-lookup"><span data-stu-id="749bb-139">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="749bb-140">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="749bb-140">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="749bb-141">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="749bb-141">Hosting model changes</span></span>

<span data-ttu-id="749bb-142">如果 `hostingModel` 设置在 web.config 文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-142">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="749bb-143">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="749bb-143">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="749bb-144">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-144">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="749bb-145">进程名</span><span class="sxs-lookup"><span data-stu-id="749bb-145">Process name</span></span>

<span data-ttu-id="749bb-146">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="749bb-146">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="749bb-147">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于将 Web 请求转发到后端 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="749bb-147">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="749bb-148">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="749bb-148">Supported Windows versions:</span></span>

* <span data-ttu-id="749bb-149">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="749bb-149">Windows 7 or later</span></span>
* <span data-ttu-id="749bb-150">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="749bb-150">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="749bb-151">该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="749bb-151">The module only works with Kestrel.</span></span> <span data-ttu-id="749bb-152">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="749bb-152">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="749bb-153">由于 ASP.NET Core 应用在独立于 IIS 辅助进程的进程中运行，因此该模块还处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="749bb-153">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="749bb-154">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="749bb-154">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="749bb-155">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的 IIS 中在进程内运行的 ASP.NET 4.x 应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="749bb-155">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="749bb-156">下图说明了 IIS、ASP.NET Core 模块和应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="749bb-156">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模块](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="749bb-158">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="749bb-158">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="749bb-159">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="749bb-159">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="749bb-160">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="749bb-160">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="749bb-161">该模块在启动时通过环境变量指定端口，IIS 集成中间件将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="749bb-161">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="749bb-162">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="749bb-162">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="749bb-163">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="749bb-163">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="749bb-164">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="749bb-164">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="749bb-165">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="749bb-165">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="749bb-166">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="749bb-166">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="749bb-167">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="749bb-167">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="749bb-168">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="749bb-168">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="749bb-169">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="749bb-169">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="749bb-170">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="749bb-170">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="749bb-171">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="749bb-171">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="749bb-172">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="749bb-172">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="749bb-173">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="749bb-173">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="749bb-174">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="749bb-174">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="749bb-175">有关如何安装和使用 ASP.NET Core 模块的说明，请参阅 <xref:host-and-deploy/iis/index>。</span><span class="sxs-lookup"><span data-stu-id="749bb-175">For instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="749bb-176">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="749bb-176">Configuration with web.config</span></span>

<span data-ttu-id="749bb-177">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="749bb-177">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="749bb-178">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="749bb-178">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet" 
                  arguments=".\MyApp.dll" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet" 
                arguments=".\MyApp.dll" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="749bb-179">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="749bb-179">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="749bb-180">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="749bb-180">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="749bb-181">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="749bb-181">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="749bb-182">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="749bb-182">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="749bb-183">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="749bb-183">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="749bb-184">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="749bb-184">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="749bb-185">特性</span><span class="sxs-lookup"><span data-stu-id="749bb-185">Attribute</span></span> | <span data-ttu-id="749bb-186">说明</span><span class="sxs-lookup"><span data-stu-id="749bb-186">Description</span></span> | <span data-ttu-id="749bb-187">默认</span><span class="sxs-lookup"><span data-stu-id="749bb-187">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="749bb-188">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-188">Optional string attribute.</span></span></p><p><span data-ttu-id="749bb-189">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="749bb-189">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="749bb-190">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-190">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="749bb-191">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="749bb-191">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="749bb-192">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-192">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="749bb-193">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-193">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="749bb-194">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="749bb-194">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="749bb-195">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-195">Optional string attribute.</span></span></p><p><span data-ttu-id="749bb-196">将托管模型指定为进程内 (`InProcess`) 或进程外 (`OutOfProcess`)。</span><span class="sxs-lookup"><span data-stu-id="749bb-196">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="749bb-197">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-197">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-198">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="749bb-198">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="749bb-199">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="749bb-199">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p> | <span data-ttu-id="749bb-200">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="749bb-200">Default: `1`</span></span><br><span data-ttu-id="749bb-201">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="749bb-201">Min: `1`</span></span><br><span data-ttu-id="749bb-202">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="749bb-202">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="749bb-203">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-203">Required string attribute.</span></span></p><p><span data-ttu-id="749bb-204">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-204">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="749bb-205">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-205">Relative paths are supported.</span></span> <span data-ttu-id="749bb-206">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="749bb-206">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="749bb-207">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-207">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-208">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="749bb-208">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="749bb-209">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-209">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="749bb-210">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="749bb-210">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="749bb-211">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="749bb-211">Default: `10`</span></span><br><span data-ttu-id="749bb-212">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="749bb-212">Min: `0`</span></span><br><span data-ttu-id="749bb-213">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="749bb-213">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="749bb-214">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-214">Optional timespan attribute.</span></span></p><p><span data-ttu-id="749bb-215">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="749bb-215">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="749bb-216">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="749bb-216">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="749bb-217">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="749bb-217">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="749bb-218">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="749bb-218">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="749bb-219">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="749bb-219">Default: `00:02:00`</span></span><br><span data-ttu-id="749bb-220">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="749bb-220">Min: `00:00:00`</span></span><br><span data-ttu-id="749bb-221">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="749bb-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="749bb-222">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-223">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="749bb-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="749bb-224">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="749bb-224">Default: `10`</span></span><br><span data-ttu-id="749bb-225">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="749bb-225">Min: `0`</span></span><br><span data-ttu-id="749bb-226">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="749bb-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="749bb-227">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-228">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="749bb-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="749bb-229">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-229">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="749bb-230">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="749bb-230">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="749bb-231">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="749bb-231">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="749bb-232">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="749bb-232">Default: `120`</span></span><br><span data-ttu-id="749bb-233">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="749bb-233">Min: `0`</span></span><br><span data-ttu-id="749bb-234">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="749bb-234">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="749bb-235">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-235">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="749bb-236">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="749bb-236">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="749bb-237">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-237">Optional string attribute.</span></span></p><p><span data-ttu-id="749bb-238">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-238">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="749bb-239">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="749bb-239">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="749bb-240">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-240">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="749bb-241">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="749bb-241">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="749bb-242">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="749bb-242">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="749bb-243">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="749bb-243">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="749bb-244">特性</span><span class="sxs-lookup"><span data-stu-id="749bb-244">Attribute</span></span> | <span data-ttu-id="749bb-245">说明</span><span class="sxs-lookup"><span data-stu-id="749bb-245">Description</span></span> | <span data-ttu-id="749bb-246">默认</span><span class="sxs-lookup"><span data-stu-id="749bb-246">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="749bb-247">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-247">Optional string attribute.</span></span></p><p><span data-ttu-id="749bb-248">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="749bb-248">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="749bb-249">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-249">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="749bb-250">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="749bb-250">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="749bb-251">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-251">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="749bb-252">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-252">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="749bb-253">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="749bb-253">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="749bb-254">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-254">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-255">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="749bb-255">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="749bb-256">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="749bb-256">Default: `1`</span></span><br><span data-ttu-id="749bb-257">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="749bb-257">Min: `1`</span></span><br><span data-ttu-id="749bb-258">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="749bb-258">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="749bb-259">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-259">Required string attribute.</span></span></p><p><span data-ttu-id="749bb-260">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-260">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="749bb-261">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-261">Relative paths are supported.</span></span> <span data-ttu-id="749bb-262">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="749bb-262">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="749bb-263">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-263">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-264">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="749bb-264">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="749bb-265">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-265">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="749bb-266">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="749bb-266">Default: `10`</span></span><br><span data-ttu-id="749bb-267">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="749bb-267">Min: `0`</span></span><br><span data-ttu-id="749bb-268">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="749bb-268">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="749bb-269">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-269">Optional timespan attribute.</span></span></p><p><span data-ttu-id="749bb-270">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="749bb-270">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="749bb-271">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="749bb-271">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="749bb-272">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="749bb-272">Default: `00:02:00`</span></span><br><span data-ttu-id="749bb-273">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="749bb-273">Min: `00:00:00`</span></span><br><span data-ttu-id="749bb-274">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="749bb-274">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="749bb-275">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-275">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-276">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="749bb-276">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="749bb-277">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="749bb-277">Default: `10`</span></span><br><span data-ttu-id="749bb-278">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="749bb-278">Min: `0`</span></span><br><span data-ttu-id="749bb-279">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="749bb-279">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="749bb-280">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-280">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-281">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="749bb-281">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="749bb-282">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-282">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="749bb-283">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="749bb-283">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="749bb-284">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="749bb-284">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="749bb-285">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="749bb-285">Default: `120`</span></span><br><span data-ttu-id="749bb-286">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="749bb-286">Min: `0`</span></span><br><span data-ttu-id="749bb-287">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="749bb-287">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="749bb-288">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-288">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="749bb-289">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="749bb-289">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="749bb-290">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-290">Optional string attribute.</span></span></p><p><span data-ttu-id="749bb-291">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-291">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="749bb-292">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="749bb-292">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="749bb-293">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-293">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="749bb-294">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="749bb-294">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="749bb-295">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="749bb-295">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="749bb-296">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="749bb-296">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="749bb-297">特性</span><span class="sxs-lookup"><span data-stu-id="749bb-297">Attribute</span></span> | <span data-ttu-id="749bb-298">说明</span><span class="sxs-lookup"><span data-stu-id="749bb-298">Description</span></span> | <span data-ttu-id="749bb-299">默认</span><span class="sxs-lookup"><span data-stu-id="749bb-299">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="749bb-300">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-300">Optional string attribute.</span></span></p><p><span data-ttu-id="749bb-301">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="749bb-301">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="749bb-302">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-302">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="749bb-303">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="749bb-303">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="749bb-304">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-304">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="749bb-305">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-305">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="749bb-306">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="749bb-306">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="749bb-307">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-307">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-308">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="749bb-308">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="749bb-309">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="749bb-309">Default: `1`</span></span><br><span data-ttu-id="749bb-310">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="749bb-310">Min: `1`</span></span><br><span data-ttu-id="749bb-311">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="749bb-311">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="749bb-312">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-312">Required string attribute.</span></span></p><p><span data-ttu-id="749bb-313">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-313">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="749bb-314">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-314">Relative paths are supported.</span></span> <span data-ttu-id="749bb-315">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="749bb-315">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="749bb-316">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-316">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-317">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="749bb-317">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="749bb-318">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-318">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="749bb-319">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="749bb-319">Default: `10`</span></span><br><span data-ttu-id="749bb-320">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="749bb-320">Min: `0`</span></span><br><span data-ttu-id="749bb-321">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="749bb-321">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="749bb-322">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-322">Optional timespan attribute.</span></span></p><p><span data-ttu-id="749bb-323">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="749bb-323">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="749bb-324">在 ASP.NET Core 2.0 或更早版本附带的 ASP.NET Core 模块版本中，必须仅使用整分钟数指定 `requestTimeout`，否则默认为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="749bb-324">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="749bb-325">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="749bb-325">Default: `00:02:00`</span></span><br><span data-ttu-id="749bb-326">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="749bb-326">Min: `00:00:00`</span></span><br><span data-ttu-id="749bb-327">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="749bb-327">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="749bb-328">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-328">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-329">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="749bb-329">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="749bb-330">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="749bb-330">Default: `10`</span></span><br><span data-ttu-id="749bb-331">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="749bb-331">Min: `0`</span></span><br><span data-ttu-id="749bb-332">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="749bb-332">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="749bb-333">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-333">Optional integer attribute.</span></span></p><p><span data-ttu-id="749bb-334">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="749bb-334">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="749bb-335">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-335">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="749bb-336">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="749bb-336">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="749bb-337">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="749bb-337">Default: `120`</span></span><br><span data-ttu-id="749bb-338">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="749bb-338">Min: `0`</span></span><br><span data-ttu-id="749bb-339">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="749bb-339">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="749bb-340">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-340">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="749bb-341">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="749bb-341">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="749bb-342">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-342">Optional string attribute.</span></span></p><p><span data-ttu-id="749bb-343">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-343">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="749bb-344">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="749bb-344">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="749bb-345">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-345">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="749bb-346">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="749bb-346">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="749bb-347">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="749bb-347">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="749bb-348">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="749bb-348">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="749bb-349">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="749bb-349">Setting environment variables</span></span>

<span data-ttu-id="749bb-350">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="749bb-350">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="749bb-351">使用 `environmentVariables` 集合元素的 `environmentVariable` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="749bb-351">Specify an environment variable with the `environmentVariable` child element of an `environmentVariables` collection element.</span></span> <span data-ttu-id="749bb-352">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="749bb-352">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="749bb-353">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="749bb-353">The following example sets two environment variables.</span></span> <span data-ttu-id="749bb-354">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="749bb-354">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="749bb-355">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="749bb-355">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="749bb-356">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-356">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="749bb-357">在不可访问不受信任的网络（如 Internet）的暂存服务器和测试服务器上，仅将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="749bb-357">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="749bb-358">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="749bb-358">app_offline.htm</span></span>

<span data-ttu-id="749bb-359">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="749bb-359">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="749bb-360">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-360">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="749bb-361">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="749bb-361">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="749bb-362">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="749bb-362">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="749bb-363">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="749bb-363">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="749bb-364">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="749bb-364">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="749bb-365">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="749bb-365">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="749bb-366">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="749bb-366">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="749bb-367">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="749bb-367">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="749bb-368">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="749bb-368">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="749bb-369">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="749bb-369">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="749bb-370">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-370">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="749bb-371">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="749bb-371">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="749bb-372">如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="749bb-372">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="749bb-373">若要禁止显示此页面并还原为默认 IIS 502 状态代码页面，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="749bb-373">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="749bb-374">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="749bb-374">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![“502.5 进程失败”状态代码页面](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="749bb-376">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="749bb-376">Log creation and redirection</span></span>

<span data-ttu-id="749bb-377">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="749bb-377">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="749bb-378">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="749bb-378">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="749bb-379">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="749bb-379">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="749bb-380">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="749bb-380">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="749bb-381">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="749bb-381">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="749bb-382">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="749bb-382">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="749bb-383">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="749bb-383">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="749bb-384">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="749bb-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="749bb-385">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="749bb-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="749bb-386">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="749bb-386">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="749bb-387">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="749bb-387">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="749bb-388">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="749bb-388">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="749bb-389">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="749bb-389">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="749bb-390">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="749bb-390">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="749bb-391">以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="749bb-391">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="749bb-392">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-392">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="749bb-393">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="749bb-393">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="749bb-394">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="749bb-394">Enhanced diagnostic logs</span></span>

<span data-ttu-id="749bb-395">可以配置 ASP.NET Core 模块提供内容以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="749bb-395">The ASP.NET Core Module provides is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="749bb-396">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="749bb-396">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value="aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="749bb-397">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="749bb-397">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="749bb-398">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="749bb-398">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="749bb-399">错误</span><span class="sxs-lookup"><span data-stu-id="749bb-399">ERROR</span></span>
* <span data-ttu-id="749bb-400">警告</span><span class="sxs-lookup"><span data-stu-id="749bb-400">WARNING</span></span>
* <span data-ttu-id="749bb-401">信息</span><span class="sxs-lookup"><span data-stu-id="749bb-401">INFO</span></span>
* <span data-ttu-id="749bb-402">TRACE</span><span class="sxs-lookup"><span data-stu-id="749bb-402">TRACE</span></span>

<span data-ttu-id="749bb-403">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="749bb-403">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="749bb-404">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="749bb-404">CONSOLE</span></span>
* <span data-ttu-id="749bb-405">事件日志</span><span class="sxs-lookup"><span data-stu-id="749bb-405">EVENTLOG</span></span>
* <span data-ttu-id="749bb-406">文件</span><span class="sxs-lookup"><span data-stu-id="749bb-406">FILE</span></span>

<span data-ttu-id="749bb-407">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="749bb-407">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="749bb-408">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; 调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="749bb-408">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="749bb-409">（默认值：aspnetcore debug.log）</span><span class="sxs-lookup"><span data-stu-id="749bb-409">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="749bb-410">`ASPNETCORE_MODULE_DEBUG` &ndash; 调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="749bb-410">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="749bb-411">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="749bb-411">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="749bb-412">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="749bb-412">The size of the log isn't limited.</span></span> <span data-ttu-id="749bb-413">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="749bb-413">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="749bb-414">有关 web.config 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="749bb-414">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="749bb-415">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="749bb-415">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="749bb-416">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="749bb-416">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="749bb-417">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="749bb-417">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="749bb-418">使用 HTTP 是一种性能优化，其中模块和 Kestrel 之间的流量发生于脱离网络接口的环回地址。</span><span class="sxs-lookup"><span data-stu-id="749bb-418">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="749bb-419">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="749bb-419">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="749bb-420">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="749bb-420">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="749bb-421">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="749bb-421">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="749bb-422">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="749bb-422">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="749bb-423">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="749bb-423">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="749bb-424">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="749bb-424">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="749bb-425">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="749bb-425">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="749bb-426">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="749bb-426">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="749bb-427">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="749bb-427">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="749bb-428">ASP.NET Core 模块安装程序使用系统帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="749bb-428">The ASP.NET Core Module installer runs with the privileges of the **SYSTEM** account.</span></span> <span data-ttu-id="749bb-429">由于本地系统帐户没有对 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 中的模块设置时，安装程序将遇到拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="749bb-429">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer hits an access denied error when attempting to configure the module settings in *applicationHost.config* on the share.</span></span> <span data-ttu-id="749bb-430">使用 IIS 共享配置时，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="749bb-430">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="749bb-431">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="749bb-431">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="749bb-432">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="749bb-432">Run the installer.</span></span>
1. <span data-ttu-id="749bb-433">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="749bb-433">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="749bb-434">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="749bb-434">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="749bb-435">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="749bb-435">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="749bb-436">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="749bb-436">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="749bb-437">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="749bb-437">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="749bb-438">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="749bb-438">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="749bb-439">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="749bb-439">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="749bb-440">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="749bb-440">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="749bb-441">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="749bb-441">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="749bb-442">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="749bb-442">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="749bb-443">模块</span><span class="sxs-lookup"><span data-stu-id="749bb-443">Module</span></span>

<span data-ttu-id="749bb-444">**IIS (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="749bb-444">**IIS (x86/amd64):**</span></span>

   * <span data-ttu-id="749bb-445">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="749bb-445">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

   * <span data-ttu-id="749bb-446">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="749bb-446">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="749bb-447">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="749bb-447">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="749bb-448">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="749bb-448">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="749bb-449">**IIS Express (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="749bb-449">**IIS Express (x86/amd64):**</span></span>

   * <span data-ttu-id="749bb-450">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="749bb-450">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

   * <span data-ttu-id="749bb-451">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="749bb-451">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="749bb-452">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="749bb-452">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="749bb-453">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="749bb-453">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="749bb-454">架构</span><span class="sxs-lookup"><span data-stu-id="749bb-454">Schema</span></span>

<span data-ttu-id="749bb-455">**IIS**</span><span class="sxs-lookup"><span data-stu-id="749bb-455">**IIS**</span></span>

   * <span data-ttu-id="749bb-456">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="749bb-456">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="749bb-457">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="749bb-457">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end
<span data-ttu-id="749bb-458">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="749bb-458">**IIS Express**</span></span>

   * <span data-ttu-id="749bb-459">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="749bb-459">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="749bb-460">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="749bb-460">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="749bb-461">配置</span><span class="sxs-lookup"><span data-stu-id="749bb-461">Configuration</span></span>

<span data-ttu-id="749bb-462">**IIS**</span><span class="sxs-lookup"><span data-stu-id="749bb-462">**IIS**</span></span>

   * <span data-ttu-id="749bb-463">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="749bb-463">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="749bb-464">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="749bb-464">**IIS Express**</span></span>

   * <span data-ttu-id="749bb-465">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="749bb-465">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>
   
   * <span data-ttu-id="749bb-466">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="749bb-466">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="749bb-467">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="749bb-467">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="749bb-468">其他资源</span><span class="sxs-lookup"><span data-stu-id="749bb-468">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="749bb-469">ASP.NET Core 模块 GitHub 存储库（引用源）</span><span class="sxs-lookup"><span data-stu-id="749bb-469">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
