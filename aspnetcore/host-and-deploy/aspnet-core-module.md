---
title: ASP.NET Core 模块
author: guardrex
description: 了解如何配置 ASP.NET Core 模块以托管 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: a33606bce6c78a19e3d380f7440e5892778806c3
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889392"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="5f157-103">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5f157-103">ASP.NET Core Module</span></span>

<span data-ttu-id="5f157-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5f157-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5f157-105">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="5f157-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="5f157-106">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="5f157-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="5f157-107">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="5f157-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="5f157-108">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="5f157-108">Supported Windows versions:</span></span>

* <span data-ttu-id="5f157-109">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5f157-109">Windows 7 or later</span></span>
* <span data-ttu-id="5f157-110">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5f157-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5f157-111">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="5f157-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5f157-112">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5f157-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="5f157-113">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="5f157-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5f157-114">托管模型</span><span class="sxs-lookup"><span data-stu-id="5f157-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5f157-115">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="5f157-115">In-process hosting model</span></span>

<span data-ttu-id="5f157-116">若要配置用于进程内托管的应用，请将 `<AspNetCoreHostingModel>` 属性添加到值为 `InProcess`（进程外托管使用 `OutOfProcess` 进行设置）的应用项目文件：</span><span class="sxs-lookup"><span data-stu-id="5f157-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5f157-117">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="5f157-117">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="5f157-118">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="5f157-118">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="5f157-119">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="5f157-119">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="5f157-120">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="5f157-120">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="5f157-121">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5f157-121">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="5f157-122">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="5f157-122">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="5f157-123">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-123">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="5f157-124">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="5f157-124">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="5f157-125">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="5f157-125">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="5f157-126">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="5f157-126">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="5f157-127">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="5f157-127">Use one app pool per app.</span></span>

* <span data-ttu-id="5f157-128">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="5f157-128">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5f157-129">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="5f157-129">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="5f157-130">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="5f157-130">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="5f157-131">如果使用 `WebHostBuilder`（而不是使用 [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)）手动设置应用的主机，并且应用曾经直接在 Kestrel 服务器上运行（自托管），则先调用 `UseKestrel`，再调用 `UseIISIntegration`。</span><span class="sxs-lookup"><span data-stu-id="5f157-131">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="5f157-132">如果顺序颠倒，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="5f157-132">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="5f157-133">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="5f157-133">Client disconnects are detected.</span></span> <span data-ttu-id="5f157-134">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="5f157-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="5f157-135">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv）。</span><span class="sxs-lookup"><span data-stu-id="5f157-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="5f157-136">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="5f157-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="5f157-137">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="5f157-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="5f157-138">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="5f157-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="5f157-139">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="5f157-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5f157-140">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="5f157-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5f157-141">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="5f157-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5f157-142">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="5f157-142">Out-of-process hosting model</span></span>

<span data-ttu-id="5f157-143">若要配置进程外托管应用，请在项目文件中使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="5f157-143">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="5f157-144">不指定 `<AspNetCoreHostingModel>` 属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-144">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="5f157-145">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="5f157-145">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="5f157-146">将 `<AspNetCoreHostingModel>` 属性的值设为 `OutOfProcess`（进程内托管设为 `InProcess`）：</span><span class="sxs-lookup"><span data-stu-id="5f157-146">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5f157-147">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="5f157-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5f157-148">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5f157-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="5f157-149">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="5f157-150">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="5f157-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="5f157-151">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="5f157-151">Hosting model changes</span></span>

<span data-ttu-id="5f157-152">如果 `hostingModel` 设置在 web.config 文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="5f157-153">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="5f157-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="5f157-154">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="5f157-155">进程名</span><span class="sxs-lookup"><span data-stu-id="5f157-155">Process name</span></span>

<span data-ttu-id="5f157-156">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="5f157-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5f157-157">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于将 Web 请求转发到后端 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="5f157-157">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="5f157-158">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="5f157-158">Supported Windows versions:</span></span>

* <span data-ttu-id="5f157-159">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5f157-159">Windows 7 or later</span></span>
* <span data-ttu-id="5f157-160">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5f157-160">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5f157-161">该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5f157-161">The module only works with Kestrel.</span></span> <span data-ttu-id="5f157-162">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="5f157-162">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="5f157-163">由于 ASP.NET Core 应用在独立于 IIS 辅助进程的进程中运行，因此该模块还处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="5f157-163">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="5f157-164">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="5f157-164">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="5f157-165">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的 IIS 中在进程内运行的 ASP.NET 4.x 应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="5f157-165">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5f157-166">下图说明了 IIS、ASP.NET Core 模块和应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="5f157-166">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模块](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="5f157-168">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="5f157-168">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5f157-169">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="5f157-169">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5f157-170">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5f157-170">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="5f157-171">该模块在启动时通过环境变量指定端口，IIS 集成中间件将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="5f157-171">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="5f157-172">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="5f157-172">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5f157-173">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="5f157-173">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5f157-174">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="5f157-174">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5f157-175">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="5f157-175">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5f157-176">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5f157-176">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5f157-177">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="5f157-177">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="5f157-178">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="5f157-178">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="5f157-179">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="5f157-179">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="5f157-180">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="5f157-180">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="5f157-181">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="5f157-181">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="5f157-182">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="5f157-182">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="5f157-183">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="5f157-183">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="5f157-184">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5f157-184">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="5f157-185">有关如何安装和使用 ASP.NET Core 模块的说明，请参阅 <xref:host-and-deploy/iis/index>。</span><span class="sxs-lookup"><span data-stu-id="5f157-185">For instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="5f157-186">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="5f157-186">Configuration with web.config</span></span>

<span data-ttu-id="5f157-187">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="5f157-187">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="5f157-188">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="5f157-188">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="5f157-189">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="5f157-189">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="5f157-190">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="5f157-190">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

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

<span data-ttu-id="5f157-191">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="5f157-191">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5f157-192">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="5f157-192">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="5f157-193">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="5f157-193">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="5f157-194">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="5f157-194">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="5f157-195">特性</span><span class="sxs-lookup"><span data-stu-id="5f157-195">Attribute</span></span> | <span data-ttu-id="5f157-196">说明</span><span class="sxs-lookup"><span data-stu-id="5f157-196">Description</span></span> | <span data-ttu-id="5f157-197">默认</span><span class="sxs-lookup"><span data-stu-id="5f157-197">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="5f157-198">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-198">Optional string attribute.</span></span></p><p><span data-ttu-id="5f157-199">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="5f157-199">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="5f157-200">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-200">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5f157-201">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="5f157-201">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="5f157-202">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-202">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5f157-203">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-203">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5f157-204">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="5f157-204">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="5f157-205">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-205">Optional string attribute.</span></span></p><p><span data-ttu-id="5f157-206">将托管模型指定为进程内 (`InProcess`) 或进程外 (`OutOfProcess`)。</span><span class="sxs-lookup"><span data-stu-id="5f157-206">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="5f157-207">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-207">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-208">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="5f157-208">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5f157-209">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="5f157-209">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="5f157-210">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="5f157-210">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5f157-211">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-211">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="5f157-212">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="5f157-212">Default: `1`</span></span><br><span data-ttu-id="5f157-213">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="5f157-213">Min: `1`</span></span><br><span data-ttu-id="5f157-214">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="5f157-214">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="5f157-215">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-215">Required string attribute.</span></span></p><p><span data-ttu-id="5f157-216">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-216">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5f157-217">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-217">Relative paths are supported.</span></span> <span data-ttu-id="5f157-218">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5f157-218">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="5f157-219">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-219">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-220">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="5f157-220">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5f157-221">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-221">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="5f157-222">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="5f157-222">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="5f157-223">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5f157-223">Default: `10`</span></span><br><span data-ttu-id="5f157-224">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5f157-224">Min: `0`</span></span><br><span data-ttu-id="5f157-225">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="5f157-225">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="5f157-226">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-226">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5f157-227">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="5f157-227">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5f157-228">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="5f157-228">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="5f157-229">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="5f157-229">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="5f157-230">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="5f157-230">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="5f157-231">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5f157-231">Default: `00:02:00`</span></span><br><span data-ttu-id="5f157-232">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5f157-232">Min: `00:00:00`</span></span><br><span data-ttu-id="5f157-233">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="5f157-233">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="5f157-234">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-235">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5f157-235">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="5f157-236">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5f157-236">Default: `10`</span></span><br><span data-ttu-id="5f157-237">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5f157-237">Min: `0`</span></span><br><span data-ttu-id="5f157-238">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="5f157-238">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="5f157-239">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-239">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-240">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5f157-240">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5f157-241">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-241">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="5f157-242">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="5f157-242">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5f157-243">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="5f157-243">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="5f157-244">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="5f157-244">Default: `120`</span></span><br><span data-ttu-id="5f157-245">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5f157-245">Min: `0`</span></span><br><span data-ttu-id="5f157-246">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="5f157-246">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="5f157-247">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-247">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5f157-248">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="5f157-248">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="5f157-249">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-249">Optional string attribute.</span></span></p><p><span data-ttu-id="5f157-250">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-250">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5f157-251">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5f157-251">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5f157-252">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-252">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5f157-253">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="5f157-253">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="5f157-254">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="5f157-254">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5f157-255">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="5f157-255">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="5f157-256">特性</span><span class="sxs-lookup"><span data-stu-id="5f157-256">Attribute</span></span> | <span data-ttu-id="5f157-257">说明</span><span class="sxs-lookup"><span data-stu-id="5f157-257">Description</span></span> | <span data-ttu-id="5f157-258">默认</span><span class="sxs-lookup"><span data-stu-id="5f157-258">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="5f157-259">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-259">Optional string attribute.</span></span></p><p><span data-ttu-id="5f157-260">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="5f157-260">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="5f157-261">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-261">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5f157-262">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="5f157-262">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="5f157-263">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-263">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5f157-264">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-264">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5f157-265">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="5f157-265">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="5f157-266">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-266">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-267">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="5f157-267">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5f157-268">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="5f157-268">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5f157-269">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-269">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="5f157-270">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="5f157-270">Default: `1`</span></span><br><span data-ttu-id="5f157-271">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="5f157-271">Min: `1`</span></span><br><span data-ttu-id="5f157-272">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="5f157-272">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="5f157-273">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-273">Required string attribute.</span></span></p><p><span data-ttu-id="5f157-274">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-274">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5f157-275">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-275">Relative paths are supported.</span></span> <span data-ttu-id="5f157-276">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5f157-276">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="5f157-277">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-277">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-278">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="5f157-278">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5f157-279">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-279">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="5f157-280">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5f157-280">Default: `10`</span></span><br><span data-ttu-id="5f157-281">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5f157-281">Min: `0`</span></span><br><span data-ttu-id="5f157-282">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="5f157-282">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="5f157-283">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-283">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5f157-284">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="5f157-284">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5f157-285">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="5f157-285">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="5f157-286">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5f157-286">Default: `00:02:00`</span></span><br><span data-ttu-id="5f157-287">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5f157-287">Min: `00:00:00`</span></span><br><span data-ttu-id="5f157-288">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="5f157-288">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="5f157-289">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-289">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-290">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5f157-290">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="5f157-291">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5f157-291">Default: `10`</span></span><br><span data-ttu-id="5f157-292">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5f157-292">Min: `0`</span></span><br><span data-ttu-id="5f157-293">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="5f157-293">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="5f157-294">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-294">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-295">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5f157-295">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5f157-296">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-296">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="5f157-297">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="5f157-297">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5f157-298">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="5f157-298">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="5f157-299">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="5f157-299">Default: `120`</span></span><br><span data-ttu-id="5f157-300">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5f157-300">Min: `0`</span></span><br><span data-ttu-id="5f157-301">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="5f157-301">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="5f157-302">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-302">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5f157-303">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="5f157-303">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="5f157-304">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-304">Optional string attribute.</span></span></p><p><span data-ttu-id="5f157-305">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-305">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5f157-306">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5f157-306">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5f157-307">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-307">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5f157-308">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5f157-308">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="5f157-309">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="5f157-309">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5f157-310">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="5f157-310">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="5f157-311">特性</span><span class="sxs-lookup"><span data-stu-id="5f157-311">Attribute</span></span> | <span data-ttu-id="5f157-312">说明</span><span class="sxs-lookup"><span data-stu-id="5f157-312">Description</span></span> | <span data-ttu-id="5f157-313">默认</span><span class="sxs-lookup"><span data-stu-id="5f157-313">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="5f157-314">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-314">Optional string attribute.</span></span></p><p><span data-ttu-id="5f157-315">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="5f157-315">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="5f157-316">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-316">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5f157-317">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="5f157-317">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="5f157-318">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-318">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5f157-319">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-319">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5f157-320">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="5f157-320">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="5f157-321">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-321">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-322">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="5f157-322">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5f157-323">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="5f157-323">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5f157-324">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-324">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="5f157-325">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="5f157-325">Default: `1`</span></span><br><span data-ttu-id="5f157-326">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="5f157-326">Min: `1`</span></span><br><span data-ttu-id="5f157-327">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="5f157-327">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="5f157-328">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-328">Required string attribute.</span></span></p><p><span data-ttu-id="5f157-329">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-329">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5f157-330">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-330">Relative paths are supported.</span></span> <span data-ttu-id="5f157-331">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5f157-331">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="5f157-332">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-332">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-333">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="5f157-333">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5f157-334">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-334">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="5f157-335">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5f157-335">Default: `10`</span></span><br><span data-ttu-id="5f157-336">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5f157-336">Min: `0`</span></span><br><span data-ttu-id="5f157-337">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="5f157-337">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="5f157-338">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-338">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5f157-339">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="5f157-339">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5f157-340">在 ASP.NET Core 2.0 或更早版本附带的 ASP.NET Core 模块版本中，必须仅使用整分钟数指定 `requestTimeout`，否则默认为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="5f157-340">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="5f157-341">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5f157-341">Default: `00:02:00`</span></span><br><span data-ttu-id="5f157-342">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5f157-342">Min: `00:00:00`</span></span><br><span data-ttu-id="5f157-343">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="5f157-343">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="5f157-344">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-344">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-345">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5f157-345">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="5f157-346">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5f157-346">Default: `10`</span></span><br><span data-ttu-id="5f157-347">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5f157-347">Min: `0`</span></span><br><span data-ttu-id="5f157-348">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="5f157-348">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="5f157-349">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-349">Optional integer attribute.</span></span></p><p><span data-ttu-id="5f157-350">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5f157-350">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5f157-351">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-351">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="5f157-352">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="5f157-352">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="5f157-353">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="5f157-353">Default: `120`</span></span><br><span data-ttu-id="5f157-354">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5f157-354">Min: `0`</span></span><br><span data-ttu-id="5f157-355">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="5f157-355">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="5f157-356">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-356">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5f157-357">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="5f157-357">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="5f157-358">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-358">Optional string attribute.</span></span></p><p><span data-ttu-id="5f157-359">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-359">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5f157-360">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5f157-360">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5f157-361">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-361">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5f157-362">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5f157-362">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="5f157-363">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="5f157-363">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5f157-364">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="5f157-364">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="5f157-365">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="5f157-365">Setting environment variables</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5f157-366">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="5f157-366">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5f157-367">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="5f157-367">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="5f157-368">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="5f157-368">Environment variables set in this section take precedence over system environment variables.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5f157-369">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="5f157-369">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5f157-370">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="5f157-370">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="5f157-371">本部分中设置的环境变量与使用同一名称设置的系统环境变量冲突。</span><span class="sxs-lookup"><span data-stu-id="5f157-371">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="5f157-372">如果在 web.config 文件以及 Windows 中的系统级别中同时设置了环境变量，则 web.config 文件中的值将被追加到系统环境变量值（例如，`ASPNETCORE_ENVIRONMENT: Development;Development`），这将阻止应用启动。</span><span class="sxs-lookup"><span data-stu-id="5f157-372">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

::: moniker-end

<span data-ttu-id="5f157-373">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="5f157-373">The following example sets two environment variables.</span></span> <span data-ttu-id="5f157-374">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="5f157-374">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="5f157-375">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="5f157-375">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="5f157-376">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-376">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="5f157-377">直接在 web.config 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml）或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="5f157-377">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="5f157-378">此方法在发布项目时设置 web.config 中的环境：</span><span class="sxs-lookup"><span data-stu-id="5f157-378">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="5f157-379">在不可访问不受信任的网络（如 Internet）的暂存服务器和测试服务器上，仅将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="5f157-379">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="5f157-380">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="5f157-380">app_offline.htm</span></span>

<span data-ttu-id="5f157-381">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="5f157-381">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="5f157-382">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-382">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="5f157-383">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="5f157-383">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="5f157-384">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="5f157-384">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5f157-385">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="5f157-385">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5f157-386">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="5f157-386">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="5f157-387">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="5f157-387">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5f157-388">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="5f157-388">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="5f157-389">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5f157-389">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="5f157-390">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5f157-390">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="5f157-391">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5f157-391">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="5f157-392">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-392">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5f157-393">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="5f157-393">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5f157-394">如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5f157-394">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="5f157-395">若要禁止显示此页面并还原为默认 IIS 502 状态代码页面，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="5f157-395">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5f157-396">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="5f157-396">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![“502.5 进程失败”状态代码页面](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="5f157-398">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="5f157-398">Log creation and redirection</span></span>

<span data-ttu-id="5f157-399">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="5f157-399">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="5f157-400">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="5f157-400">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="5f157-401">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="5f157-401">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5f157-402">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="5f157-402">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="5f157-403">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="5f157-403">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="5f157-404">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="5f157-404">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="5f157-405">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="5f157-405">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="5f157-406">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="5f157-406">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5f157-407">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="5f157-407">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="5f157-408">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="5f157-408">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="5f157-409">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="5f157-409">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="5f157-410">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="5f157-410">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5f157-411">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="5f157-411">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="5f157-412">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="5f157-412">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="5f157-413">以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="5f157-413">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="5f157-414">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-414">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="5f157-415">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="5f157-415">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

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

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="5f157-416">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="5f157-416">Enhanced diagnostic logs</span></span>

<span data-ttu-id="5f157-417">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="5f157-417">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="5f157-418">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="5f157-418">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="5f157-419">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="5f157-419">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="5f157-420">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="5f157-420">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="5f157-421">错误</span><span class="sxs-lookup"><span data-stu-id="5f157-421">ERROR</span></span>
* <span data-ttu-id="5f157-422">警告</span><span class="sxs-lookup"><span data-stu-id="5f157-422">WARNING</span></span>
* <span data-ttu-id="5f157-423">信息</span><span class="sxs-lookup"><span data-stu-id="5f157-423">INFO</span></span>
* <span data-ttu-id="5f157-424">TRACE</span><span class="sxs-lookup"><span data-stu-id="5f157-424">TRACE</span></span>

<span data-ttu-id="5f157-425">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="5f157-425">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="5f157-426">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="5f157-426">CONSOLE</span></span>
* <span data-ttu-id="5f157-427">事件日志</span><span class="sxs-lookup"><span data-stu-id="5f157-427">EVENTLOG</span></span>
* <span data-ttu-id="5f157-428">文件</span><span class="sxs-lookup"><span data-stu-id="5f157-428">FILE</span></span>

<span data-ttu-id="5f157-429">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="5f157-429">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="5f157-430">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; 调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5f157-430">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="5f157-431">（默认值：aspnetcore debug.log）</span><span class="sxs-lookup"><span data-stu-id="5f157-431">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="5f157-432">`ASPNETCORE_MODULE_DEBUG` &ndash; 调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="5f157-432">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="5f157-433">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="5f157-433">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="5f157-434">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="5f157-434">The size of the log isn't limited.</span></span> <span data-ttu-id="5f157-435">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="5f157-435">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="5f157-436">有关 web.config 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="5f157-436">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="5f157-437">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="5f157-437">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5f157-438">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="5f157-438">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="5f157-439">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="5f157-439">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="5f157-440">使用 HTTP 是一种性能优化，其中模块和 Kestrel 之间的流量发生于脱离网络接口的环回地址。</span><span class="sxs-lookup"><span data-stu-id="5f157-440">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="5f157-441">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="5f157-441">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="5f157-442">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="5f157-442">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="5f157-443">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="5f157-443">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="5f157-444">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="5f157-444">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="5f157-445">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="5f157-445">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="5f157-446">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="5f157-446">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="5f157-447">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="5f157-447">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="5f157-448">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="5f157-448">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="5f157-449">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5f157-449">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="5f157-450">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="5f157-450">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="5f157-451">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="5f157-451">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5f157-452">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="5f157-452">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="5f157-453">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="5f157-453">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="5f157-454">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="5f157-454">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5f157-455">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="5f157-455">Run the installer.</span></span>
1. <span data-ttu-id="5f157-456">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="5f157-456">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5f157-457">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="5f157-457">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5f157-458">使用 IIS 共享配置时，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="5f157-458">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="5f157-459">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="5f157-459">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5f157-460">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="5f157-460">Run the installer.</span></span>
1. <span data-ttu-id="5f157-461">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="5f157-461">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5f157-462">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="5f157-462">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization"></a><span data-ttu-id="5f157-463">应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="5f157-463">Application Initialization</span></span>

<span data-ttu-id="5f157-464">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="5f157-464">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="5f157-465">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="5f157-465">The request triggers the app to start.</span></span> <span data-ttu-id="5f157-466">应用程序初始化可由[进程内托管模型](xref:fundamentals/servers/index#in-process-hosting-model)和[进程外托管模型](xref:fundamentals/servers/index#out-of-process-hosting-model)与 ASP.NET Core 模块版本 2 一起使用。</span><span class="sxs-lookup"><span data-stu-id="5f157-466">Application Initialization can be used by both the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model) and [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model) with the ASP.NET Core Module version 2.</span></span>

<span data-ttu-id="5f157-467">启用应用程序初始化：</span><span class="sxs-lookup"><span data-stu-id="5f157-467">To enable Application Initialization:</span></span>

1. <span data-ttu-id="5f157-468">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="5f157-468">Confirm that the IIS Application Initialization role feature in enabled:</span></span>
   * <span data-ttu-id="5f157-469">在 Windows 7 或更高版本上：导航到“控制面板” > “程序” > “程序和功能” > “打开或关闭 Windows 功能”（位于屏幕左侧）。</span><span class="sxs-lookup"><span data-stu-id="5f157-469">On Windows 7 or later: Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span> <span data-ttu-id="5f157-470">打开“Internet Information Services” > “万维网服务” > “应用程序开发功能”。</span><span class="sxs-lookup"><span data-stu-id="5f157-470">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="5f157-471">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="5f157-471">Select the check box for **Application Initialization**.</span></span>
   * <span data-ttu-id="5f157-472">在 Windows Server 2008 R2 或更高版本上，打开“添加角色和功能向导”。</span><span class="sxs-lookup"><span data-stu-id="5f157-472">On Windows Server 2008 R2 or later, open the **Add Roles and Features Wizard**.</span></span> <span data-ttu-id="5f157-473">访问“选择角色服务”面板时，打开“应用程序开发”节点并选中“应用程序初始化”复选框。</span><span class="sxs-lookup"><span data-stu-id="5f157-473">When you reach the **Select role services** panel, open the **Application Development** node and select the **Application Initialization** check box.</span></span>
1. <span data-ttu-id="5f157-474">在 IIS 管理器的“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="5f157-474">In IIS Manager, select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="5f157-475">在列表中选择应用的应用池。</span><span class="sxs-lookup"><span data-stu-id="5f157-475">Select the app's app pool in the list.</span></span>
1. <span data-ttu-id="5f157-476">在“操作”面板中的“编辑应用程序池”下选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="5f157-476">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="5f157-477">将“启动模式”设置为“AlwaysRunning”。</span><span class="sxs-lookup"><span data-stu-id="5f157-477">Set **Start Mode** to **AlwaysRunning**.</span></span>
1. <span data-ttu-id="5f157-478">打开“连接”面板中的“网站”节点。</span><span class="sxs-lookup"><span data-stu-id="5f157-478">Open the **Sites** node in the **Connections** panel.</span></span>
1. <span data-ttu-id="5f157-479">选择该应用。</span><span class="sxs-lookup"><span data-stu-id="5f157-479">Select the app.</span></span>
1. <span data-ttu-id="5f157-480">在“操作”面板中的“管理网站”下选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="5f157-480">Select **Advanced Settings** under **Manage Website** in the **Actions** panel.</span></span>
1. <span data-ttu-id="5f157-481">将“预加载已启用”设置为“True”。</span><span class="sxs-lookup"><span data-stu-id="5f157-481">Set **Preload Enabled** to **True**.</span></span>

<span data-ttu-id="5f157-482">若要了解详细信息，请参阅 [IIS 8.0 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)。</span><span class="sxs-lookup"><span data-stu-id="5f157-482">For more information, see [IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization).</span></span>

<span data-ttu-id="5f157-483">使用[进程外托管模型](xref:fundamentals/servers/index#out-of-process-hosting-model)的应用必须使用外部服务定期 ping 应用，以使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="5f157-483">Apps that use the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model) must use an external service to periodically ping the app in order to keep it running.</span></span>

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="5f157-484">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="5f157-484">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="5f157-485">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5f157-485">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="5f157-486">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="5f157-486">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="5f157-487">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="5f157-487">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="5f157-488">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="5f157-488">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="5f157-489">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="5f157-489">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="5f157-490">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="5f157-490">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="5f157-491">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="5f157-491">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="5f157-492">模块</span><span class="sxs-lookup"><span data-stu-id="5f157-492">Module</span></span>

<span data-ttu-id="5f157-493">**IIS (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="5f157-493">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="5f157-494">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5f157-494">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5f157-495">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5f157-495">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5f157-496">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5f157-496">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5f157-497">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5f157-497">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="5f157-498">**IIS Express (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="5f157-498">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="5f157-499">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5f157-499">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5f157-500">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5f157-500">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5f157-501">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5f157-501">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5f157-502">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5f157-502">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="5f157-503">架构</span><span class="sxs-lookup"><span data-stu-id="5f157-503">Schema</span></span>

<span data-ttu-id="5f157-504">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5f157-504">**IIS**</span></span>

* <span data-ttu-id="5f157-505">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5f157-505">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5f157-506">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5f157-506">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

<span data-ttu-id="5f157-507">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5f157-507">**IIS Express**</span></span>

* <span data-ttu-id="5f157-508">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5f157-508">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5f157-509">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5f157-509">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="5f157-510">Configuration</span><span class="sxs-lookup"><span data-stu-id="5f157-510">Configuration</span></span>

<span data-ttu-id="5f157-511">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5f157-511">**IIS**</span></span>

* <span data-ttu-id="5f157-512">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5f157-512">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="5f157-513">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5f157-513">**IIS Express**</span></span>

* <span data-ttu-id="5f157-514">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5f157-514">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="5f157-515">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="5f157-515">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="5f157-516">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="5f157-516">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f157-517">其他资源</span><span class="sxs-lookup"><span data-stu-id="5f157-517">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="5f157-518">ASP.NET Core 模块 GitHub 存储库（引用源）</span><span class="sxs-lookup"><span data-stu-id="5f157-518">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
