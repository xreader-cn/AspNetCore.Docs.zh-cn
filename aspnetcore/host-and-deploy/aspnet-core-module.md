---
title: ASP.NET Core 模块
author: rick-anderson
description: 了解用于通过 IIS 托管 ASP.NET Core 应用的 ASP.NET Core 模块。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: d0e6c0c31890c58aaca936fc6f1e92cb9a1ab456
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901231"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="f6b7a-103">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-103">ASP.NET Core Module</span></span>

<span data-ttu-id="f6b7a-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti) 和 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="f6b7a-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f6b7a-105">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，能让 ASP.NET Core 应用程序通过 IIS 运行。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="f6b7a-106">使用以下任一方式通过 IIS 运行 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="f6b7a-107">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](xref:host-and-deploy/iis/in-process-hosting)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="f6b7a-108">将 Web 请求转发到运行 Kestrel 服务器的后端 ASP.NET Core 应用，称为[进程外托管模型](xref:host-and-deploy/iis/out-of-process-hosting)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="f6b7a-109">我们需要对这两类托管模型进行权衡。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="f6b7a-110">默认情况下使用的是进程内托管模型，因为这样可以得到更好的性能和诊断。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="f6b7a-111">安装 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="f6b7a-112">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="f6b7a-113">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="f6b7a-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="f6b7a-114">若要详细了解如何安装 ASP.NET Core 模块或不同版本的模块，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-114">For more details instructions on how to install the ASP.NET Core Module, or installing different versions of the module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

<span data-ttu-id="f6b7a-115">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-115">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="f6b7a-116">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-116">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="f6b7a-117">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-117">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="f6b7a-118">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-118">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="f6b7a-119">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-119">Supported Windows versions:</span></span>

* <span data-ttu-id="f6b7a-120">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f6b7a-120">Windows 7 or later</span></span>
* <span data-ttu-id="f6b7a-121">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f6b7a-121">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="f6b7a-122">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-122">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f6b7a-123">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-123">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="f6b7a-124">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-124">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f6b7a-125">托管模型</span><span class="sxs-lookup"><span data-stu-id="f6b7a-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f6b7a-126">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="f6b7a-126">In-process hosting model</span></span>

<span data-ttu-id="f6b7a-127">ASP.NET Core 应用默认为进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-127">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="f6b7a-128">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-128">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="f6b7a-129">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-129">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="f6b7a-130">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-130">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="f6b7a-131">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-131">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="f6b7a-132">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-132">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="f6b7a-133">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-133">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="f6b7a-134">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-134">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="f6b7a-135">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-135">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="f6b7a-136">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-136">Use one app pool per app.</span></span>

* <span data-ttu-id="f6b7a-137">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [`app_offline.htm` 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-137">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f6b7a-138">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-138">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="f6b7a-139">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-139">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="f6b7a-140">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-140">Client disconnects are detected.</span></span> <span data-ttu-id="f6b7a-141">客户端断开连接时，将取消 [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-141">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="f6b7a-142">在 ASP.NET Core 2.2.1 或更早版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 所启动进程的工作目录而非应用目录（例如，对于 `w3wp.exe`，是 `C:\Windows\System32\inetsrv`）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-142">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="f6b7a-143">对于设置应用的当前目录的示例代码，请参阅 [`CurrentDirectoryHelpers` 类](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-143">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="f6b7a-144">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-144">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="f6b7a-145">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-145">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="f6b7a-146">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-146">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f6b7a-147">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-147">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f6b7a-148">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-148">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="f6b7a-149">不支持 [Web 包（单文件）部署](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-149">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f6b7a-150">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="f6b7a-150">Out-of-process hosting model</span></span>

<span data-ttu-id="f6b7a-151">若要配置进程外托管应用，请在项目文件 ( `.csproj`) 中将 `<AspNetCoreHostingModel>` 属性的值设置为 `OutOfProcess`：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-151">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f6b7a-152">进程内托管设为 `InProcess`，这是默认值。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-152">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="f6b7a-153">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-153">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="f6b7a-154">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-154">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f6b7a-155">对于进程外托管，[`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) 会调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 来进行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-155">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="f6b7a-156">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-156">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="f6b7a-157">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-157">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="f6b7a-158">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="f6b7a-158">Hosting model changes</span></span>

<span data-ttu-id="f6b7a-159">如果在 `web.config` 文件中更改了 `hostingModel` 设置（如 [`web.config`web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-159">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="f6b7a-160">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-160">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="f6b7a-161">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-161">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="f6b7a-162">进程名</span><span class="sxs-lookup"><span data-stu-id="f6b7a-162">Process name</span></span>

<span data-ttu-id="f6b7a-163">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-163">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="f6b7a-164">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-164">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f6b7a-165">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-165">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f6b7a-166">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-166">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f6b7a-167">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-167">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f6b7a-168">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-168">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f6b7a-169">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-169">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f6b7a-170">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-170">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f6b7a-171">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-171">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f6b7a-172">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="f6b7a-172">Configuration with web.config</span></span>

<span data-ttu-id="f6b7a-173">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-173">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f6b7a-174">以下 `web.config` 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-174">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="f6b7a-175">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-175">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="f6b7a-176">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-176">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="f6b7a-177">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-177">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f6b7a-178">该路径会将 stdout 日志保存到 `LogFiles` 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-178">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f6b7a-179">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-179">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f6b7a-180">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="f6b7a-180">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f6b7a-181">特性</span><span class="sxs-lookup"><span data-stu-id="f6b7a-181">Attribute</span></span> | <span data-ttu-id="f6b7a-182">描述</span><span class="sxs-lookup"><span data-stu-id="f6b7a-182">Description</span></span> | <span data-ttu-id="f6b7a-183">默认</span><span class="sxs-lookup"><span data-stu-id="f6b7a-183">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f6b7a-184">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-184">Optional string attribute.</span></span></p><p><span data-ttu-id="f6b7a-185">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-185">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f6b7a-186">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-186">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f6b7a-187">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-187">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f6b7a-188">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f6b7a-189">如果为 true，会将令牌作为每个请求的标头 `'MS-ASPNETCORE-WINAUTHTOKEN'` 转发到在 `%ASPNETCORE_PORT%` 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-189">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="f6b7a-190">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-190">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="f6b7a-191">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-191">Optional string attribute.</span></span></p><p><span data-ttu-id="f6b7a-192">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-192">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="f6b7a-193">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-194">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-194">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f6b7a-195">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-195">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="f6b7a-196">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-196">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f6b7a-197">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-197">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="f6b7a-198">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-198">Default: `1`</span></span><br><span data-ttu-id="f6b7a-199">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-199">Min: `1`</span></span><br><span data-ttu-id="f6b7a-200">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="f6b7a-200">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="f6b7a-201">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-201">Required string attribute.</span></span></p><p><span data-ttu-id="f6b7a-202">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-202">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f6b7a-203">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-203">Relative paths are supported.</span></span> <span data-ttu-id="f6b7a-204">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-204">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f6b7a-205">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-205">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-206">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-206">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f6b7a-207">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-207">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="f6b7a-208">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-208">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="f6b7a-209">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-209">Default: `10`</span></span><br><span data-ttu-id="f6b7a-210">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-210">Min: `0`</span></span><br><span data-ttu-id="f6b7a-211">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-211">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f6b7a-212">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-212">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f6b7a-213">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-213">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f6b7a-214">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-214">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="f6b7a-215">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-215">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="f6b7a-216">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-216">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="f6b7a-217">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-217">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="f6b7a-218">在分钟或秒钟值中使用“60”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-218">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="f6b7a-219">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-219">Default: `00:02:00`</span></span><br><span data-ttu-id="f6b7a-220">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-220">Min: `00:00:00`</span></span><br><span data-ttu-id="f6b7a-221">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f6b7a-222">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-223">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f6b7a-224">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-224">Default: `10`</span></span><br><span data-ttu-id="f6b7a-225">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-225">Min: `0`</span></span><br><span data-ttu-id="f6b7a-226">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f6b7a-227">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-228">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f6b7a-229">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-229">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="f6b7a-230">进程内托管时：不会重新启动该进程，也不会使用 rapidFailsPerMinute 设置  。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-230">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="f6b7a-231">进程外托管时：模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-231">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f6b7a-232">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-232">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f6b7a-233">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-233">Default: `120`</span></span><br><span data-ttu-id="f6b7a-234">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-234">Min: `0`</span></span><br><span data-ttu-id="f6b7a-235">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-235">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f6b7a-236">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-236">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f6b7a-237">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-237">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f6b7a-238">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-238">Optional string attribute.</span></span></p><p><span data-ttu-id="f6b7a-239">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-239">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f6b7a-240">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-240">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f6b7a-241">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-241">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f6b7a-242">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-242">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="f6b7a-243">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (`.log`) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-243">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f6b7a-244">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 `logs` 文件夹中保存为 `stdout_20180205194132_1934.log`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-244">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="f6b7a-245">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="f6b7a-245">Set environment variables</span></span>

<span data-ttu-id="f6b7a-246">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-246">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f6b7a-247">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-247">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="f6b7a-248">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-248">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="f6b7a-249">以下示例在 `web.config` 中设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-249">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="f6b7a-250">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-250">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f6b7a-251">开发人员可能会暂时在 `web.config` 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-251">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f6b7a-252">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-252">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="f6b7a-253">直接在 `web.config` 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-253">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="f6b7a-254">此方法在发布项目时设置 `web.config` 中的环境：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-254">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="f6b7a-255">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-255">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="f6b7a-256">如果在应用的根目录中检测到名为“`app_offline.htm`”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-256">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f6b7a-257">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-257">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f6b7a-258">存在 `app_offline.htm` 文件时，ASP.NET Core 模块会通过发送回 `app_offline.htm` 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-258">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="f6b7a-259">删除 `app_offline.htm` 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-259">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="f6b7a-260">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-260">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f6b7a-261">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-261">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f6b7a-262">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="f6b7a-262">Start-up error page</span></span>

<span data-ttu-id="f6b7a-263">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-263">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="f6b7a-264">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-264">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="f6b7a-265">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-265">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="f6b7a-266">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-266">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="f6b7a-267">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-267">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f6b7a-268">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-268">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f6b7a-269">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="f6b7a-269">Log creation and redirection</span></span>

<span data-ttu-id="f6b7a-270">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-270">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f6b7a-271">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-271">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f6b7a-272">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-272">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f6b7a-273">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-273">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f6b7a-274">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-274">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f6b7a-275">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-275">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="f6b7a-276">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-276">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f6b7a-277">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-277">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f6b7a-278">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-278">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f6b7a-279">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-279">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f6b7a-280">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (`.log`) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 `stdout`）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-280">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="f6b7a-281">如果 `stdoutLogFile` 路径以 `stdout` 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 `stdout_20180205194132_1934.log`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-281">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="f6b7a-282">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-282">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f6b7a-283">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-283">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="f6b7a-284">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-284">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="f6b7a-285">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-285">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="f6b7a-286">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-286">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f6b7a-287">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-287">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="f6b7a-288">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-288">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="f6b7a-289">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-289">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f6b7a-290">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="f6b7a-290">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f6b7a-291">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-291">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f6b7a-292">将 `<handlerSettings>` 元素添加到 `web.config` 中的 `<aspNetCore>` 元素。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-292">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="f6b7a-293">将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-293">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="f6b7a-294">创建日志文件时，路径中的任何文件夹（上述示例中为 `logs`）由模块创建。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-294">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="f6b7a-295">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\{APP POOL NAME}` 提供写入权限，其中占位符 `{APP POOL NAME}` 为应用池名称）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-295">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="f6b7a-296">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-296">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f6b7a-297">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-297">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f6b7a-298">错误</span><span class="sxs-lookup"><span data-stu-id="f6b7a-298">ERROR</span></span>
* <span data-ttu-id="f6b7a-299">警告</span><span class="sxs-lookup"><span data-stu-id="f6b7a-299">WARNING</span></span>
* <span data-ttu-id="f6b7a-300">INFO</span><span class="sxs-lookup"><span data-stu-id="f6b7a-300">INFO</span></span>
* <span data-ttu-id="f6b7a-301">TRACE</span><span class="sxs-lookup"><span data-stu-id="f6b7a-301">TRACE</span></span>

<span data-ttu-id="f6b7a-302">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-302">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f6b7a-303">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="f6b7a-303">CONSOLE</span></span>
* <span data-ttu-id="f6b7a-304">事件日志</span><span class="sxs-lookup"><span data-stu-id="f6b7a-304">EVENTLOG</span></span>
* <span data-ttu-id="f6b7a-305">文件</span><span class="sxs-lookup"><span data-stu-id="f6b7a-305">FILE</span></span>

<span data-ttu-id="f6b7a-306">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-306">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f6b7a-307">`ASPNETCORE_MODULE_DEBUG_FILE`：调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="f6b7a-308">（默认值：`aspnetcore-debug.log`）</span><span class="sxs-lookup"><span data-stu-id="f6b7a-308">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="f6b7a-309">`ASPNETCORE_MODULE_DEBUG`：调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-309">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f6b7a-310">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-310">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f6b7a-311">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-311">The size of the log isn't limited.</span></span> <span data-ttu-id="f6b7a-312">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-312">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="f6b7a-313">有关 `web.config` 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-313">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="f6b7a-314">修改堆栈大小</span><span class="sxs-lookup"><span data-stu-id="f6b7a-314">Modify the stack size</span></span>

<span data-ttu-id="f6b7a-315">*仅适用于使用进程内托管模型的情况。*</span><span class="sxs-lookup"><span data-stu-id="f6b7a-315">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="f6b7a-316">在 `web.config` 中使用 `stackSize` 设置（以字节为单位）配置托管堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-316">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="f6b7a-317">默认大小为 1,048,576 字节 (1 MB)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-317">The default size is 1,048,576 bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f6b7a-318">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="f6b7a-318">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f6b7a-319">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="f6b7a-319">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="f6b7a-320">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-320">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f6b7a-321">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-321">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f6b7a-322">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-322">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f6b7a-323">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-323">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f6b7a-324">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-324">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f6b7a-325">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-325">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f6b7a-326">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-326">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f6b7a-327">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-327">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f6b7a-328">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-328">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f6b7a-329">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-329">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f6b7a-330">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-330">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f6b7a-331">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 `applicationHost.config` 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-331">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="f6b7a-332">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-332">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="f6b7a-333">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-333">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="f6b7a-334">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-334">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f6b7a-335">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-335">Run the installer.</span></span>
1. <span data-ttu-id="f6b7a-336">将已更新的 `applicationHost.config` 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-336">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="f6b7a-337">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-337">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f6b7a-338">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="f6b7a-338">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f6b7a-339">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-339">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f6b7a-340">在托管系统上，导航到 `%windir%\System32\inetsrv`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-340">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="f6b7a-341">找到 `aspnetcore.dll` 文件。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-341">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="f6b7a-342">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-342">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f6b7a-343">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-343">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f6b7a-344">可在 `C:\Users\%UserName%\AppData\Local\Temp` 中找到模块的托管捆绑安装程序日志。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-344">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="f6b7a-345">该文件命名为 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-345">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f6b7a-346">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="f6b7a-346">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f6b7a-347">模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-347">Module</span></span>

<span data-ttu-id="f6b7a-348">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-348">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="f6b7a-349">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-349">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="f6b7a-350">架构</span><span class="sxs-lookup"><span data-stu-id="f6b7a-350">Schema</span></span>

<span data-ttu-id="f6b7a-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-351">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="f6b7a-352">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-352">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="f6b7a-353">Configuration</span><span class="sxs-lookup"><span data-stu-id="f6b7a-353">Configuration</span></span>

<span data-ttu-id="f6b7a-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="f6b7a-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-355">**IIS Express**</span></span>

* <span data-ttu-id="f6b7a-356">Visual Studio：`{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="f6b7a-357">iisexpress.exe CLI：`%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="f6b7a-358">通过在 `applicationHost.config` 文件中搜索 `aspnetcore` 可以找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-358">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f6b7a-359">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-359">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="f6b7a-360">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-360">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="f6b7a-361">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-361">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="f6b7a-362">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-362">Supported Windows versions:</span></span>

* <span data-ttu-id="f6b7a-363">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f6b7a-363">Windows 7 or later</span></span>
* <span data-ttu-id="f6b7a-364">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f6b7a-364">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f6b7a-365">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-365">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f6b7a-366">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-366">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="f6b7a-367">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-367">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f6b7a-368">托管模型</span><span class="sxs-lookup"><span data-stu-id="f6b7a-368">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f6b7a-369">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="f6b7a-369">In-process hosting model</span></span>

<span data-ttu-id="f6b7a-370">若要配置用于进程内托管的应用，请将 `<AspNetCoreHostingModel>` 属性添加到值为 `InProcess`（进程外托管使用 `OutOfProcess` 进行设置）的应用项目文件：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-370">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f6b7a-371">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-371">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="f6b7a-372">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-372">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="f6b7a-373">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-373">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="f6b7a-374">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-374">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="f6b7a-375">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-375">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="f6b7a-376">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-376">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="f6b7a-377">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-377">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="f6b7a-378">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-378">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="f6b7a-379">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-379">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="f6b7a-380">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-380">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="f6b7a-381">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-381">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="f6b7a-382">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-382">Use one app pool per app.</span></span>

* <span data-ttu-id="f6b7a-383">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-383">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f6b7a-384">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-384">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="f6b7a-385">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-385">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="f6b7a-386">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-386">Client disconnects are detected.</span></span> <span data-ttu-id="f6b7a-387">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-387">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="f6b7a-388">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv ）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-388">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="f6b7a-389">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-389">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="f6b7a-390">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-390">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="f6b7a-391">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-391">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="f6b7a-392">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-392">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f6b7a-393">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-393">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f6b7a-394">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-394">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f6b7a-395">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="f6b7a-395">Out-of-process hosting model</span></span>

<span data-ttu-id="f6b7a-396">若要配置进程外托管应用，请在项目文件中使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-396">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="f6b7a-397">不指定 `<AspNetCoreHostingModel>` 属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-397">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="f6b7a-398">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-398">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="f6b7a-399">将 `<AspNetCoreHostingModel>` 属性的值设为 `OutOfProcess`（进程内托管设为 `InProcess`）：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-399">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f6b7a-400">值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-400">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="f6b7a-401">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-401">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f6b7a-402">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-402">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="f6b7a-403">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-403">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="f6b7a-404">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-404">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="f6b7a-405">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="f6b7a-405">Hosting model changes</span></span>

<span data-ttu-id="f6b7a-406">如果 `hostingModel` 设置在 web.config 文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-406">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="f6b7a-407">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-407">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="f6b7a-408">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-408">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="f6b7a-409">进程名</span><span class="sxs-lookup"><span data-stu-id="f6b7a-409">Process name</span></span>

<span data-ttu-id="f6b7a-410">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-410">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="f6b7a-411">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-411">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f6b7a-412">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-412">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f6b7a-413">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-413">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f6b7a-414">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-414">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f6b7a-415">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-415">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f6b7a-416">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-416">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f6b7a-417">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-417">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f6b7a-418">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-418">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f6b7a-419">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="f6b7a-419">Configuration with web.config</span></span>

<span data-ttu-id="f6b7a-420">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-420">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f6b7a-421">以下 *web.config* 文件发布用于 [依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-421">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="f6b7a-422">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-422">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="f6b7a-423">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-423">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="f6b7a-424">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-424">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f6b7a-425">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-425">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f6b7a-426">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-426">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f6b7a-427">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="f6b7a-427">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f6b7a-428">特性</span><span class="sxs-lookup"><span data-stu-id="f6b7a-428">Attribute</span></span> | <span data-ttu-id="f6b7a-429">描述</span><span class="sxs-lookup"><span data-stu-id="f6b7a-429">Description</span></span> | <span data-ttu-id="f6b7a-430">默认</span><span class="sxs-lookup"><span data-stu-id="f6b7a-430">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f6b7a-431">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-431">Optional string attribute.</span></span></p><p><span data-ttu-id="f6b7a-432">`processPath` 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-432">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f6b7a-433">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f6b7a-434">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-434">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f6b7a-435">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-435">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f6b7a-436">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-436">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f6b7a-437">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-437">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="f6b7a-438">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-438">Optional string attribute.</span></span></p><p><span data-ttu-id="f6b7a-439">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-439">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="f6b7a-440">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-440">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-441">指定每个应用均可启动的 `processPath` 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-441">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f6b7a-442">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-442">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="f6b7a-443">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-443">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f6b7a-444">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-444">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="f6b7a-445">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-445">Default: `1`</span></span><br><span data-ttu-id="f6b7a-446">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-446">Min: `1`</span></span><br><span data-ttu-id="f6b7a-447">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="f6b7a-447">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="f6b7a-448">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-448">Required string attribute.</span></span></p><p><span data-ttu-id="f6b7a-449">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-449">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f6b7a-450">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-450">Relative paths are supported.</span></span> <span data-ttu-id="f6b7a-451">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-451">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f6b7a-452">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-452">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-453">指定允许 `processPath` 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-453">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="f6b7a-454">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-454">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="f6b7a-455">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-455">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="f6b7a-456">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-456">Default: `10`</span></span><br><span data-ttu-id="f6b7a-457">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-457">Min: `0`</span></span><br><span data-ttu-id="f6b7a-458">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-458">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f6b7a-459">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-459">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f6b7a-460">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-460">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f6b7a-461">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-461">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="f6b7a-462">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-462">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="f6b7a-463">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-463">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="f6b7a-464">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-464">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="f6b7a-465">在分钟或秒钟值中使用“60”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-465">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="f6b7a-466">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-466">Default: `00:02:00`</span></span><br><span data-ttu-id="f6b7a-467">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-467">Min: `00:00:00`</span></span><br><span data-ttu-id="f6b7a-468">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-468">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f6b7a-469">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-469">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-470">检测到 `app_offline.htm` 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-470">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="f6b7a-471">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-471">Default: `10`</span></span><br><span data-ttu-id="f6b7a-472">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-472">Min: `0`</span></span><br><span data-ttu-id="f6b7a-473">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-473">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f6b7a-474">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-474">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-475">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-475">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f6b7a-476">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-476">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="f6b7a-477">进程内托管时：不会重新启动该进程，也不会使用 `rapidFailsPerMinute` 设置 。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-477">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="f6b7a-478">进程外托管时：模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 `rapidFailsPerMinute` 次。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-478">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f6b7a-479">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-479">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f6b7a-480">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-480">Default: `120`</span></span><br><span data-ttu-id="f6b7a-481">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-481">Min: `0`</span></span><br><span data-ttu-id="f6b7a-482">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-482">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f6b7a-483">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-483">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f6b7a-484">如果为 true，`processPath` 中指定的进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件  。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-484">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f6b7a-485">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-485">Optional string attribute.</span></span></p><p><span data-ttu-id="f6b7a-486">指定在其中记录 `processPath` 中指定进程的 `stdout` 和 `stderr` 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-486">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="f6b7a-487">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-487">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f6b7a-488">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-488">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f6b7a-489">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-489">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="f6b7a-490">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (`.log`) 添加到 `stdoutLogFile` 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-490">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="f6b7a-491">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 `logs` 文件夹中保存为 `stdout_20180205194132_1934.log`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-491">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="f6b7a-492">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="f6b7a-492">Setting environment variables</span></span>

<span data-ttu-id="f6b7a-493">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-493">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f6b7a-494">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-494">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="f6b7a-495">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-495">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="f6b7a-496">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-496">The following example sets two environment variables.</span></span> <span data-ttu-id="f6b7a-497">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-497">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f6b7a-498">开发人员可能会暂时在 `web.config` 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-498">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f6b7a-499">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-499">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="f6b7a-500">直接在 `web.config` 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-500">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="f6b7a-501">此方法在发布项目时设置 `web.config` 中的环境：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-501">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="f6b7a-502">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-502">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f6b7a-503">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="f6b7a-503">app_offline.htm</span></span>

<span data-ttu-id="f6b7a-504">如果在应用的根目录中检测到名为“`app_offline.htm`”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-504">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f6b7a-505">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-505">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f6b7a-506">存在 `app_offline.htm` 文件时，ASP.NET Core 模块会通过发送回 `app_offline.htm` 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-506">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="f6b7a-507">删除 `app_offline.htm` 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-507">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="f6b7a-508">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-508">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f6b7a-509">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-509">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f6b7a-510">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="f6b7a-510">Start-up error page</span></span>

<span data-ttu-id="f6b7a-511">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-511">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="f6b7a-512">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-512">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="f6b7a-513">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-513">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="f6b7a-514">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-514">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="f6b7a-515">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-515">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f6b7a-516">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-516">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f6b7a-517">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="f6b7a-517">Log creation and redirection</span></span>

<span data-ttu-id="f6b7a-518">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-518">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f6b7a-519">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-519">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f6b7a-520">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\{APP POOL NAME}` 提供写入权限，其中占位符 `{APP POOL NAME}` 为应用池名称）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-520">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="f6b7a-521">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-521">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f6b7a-522">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-522">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f6b7a-523">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-523">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="f6b7a-524">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-524">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f6b7a-525">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-525">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f6b7a-526">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-526">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f6b7a-527">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-527">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f6b7a-528">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (`.log`) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 `stdout`）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-528">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="f6b7a-529">如果 `stdoutLogFile` 路径以 `stdout` 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 `stdout_20180205194132_1934.log`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-529">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="f6b7a-530">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-530">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f6b7a-531">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-531">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="f6b7a-532">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-532">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="f6b7a-533">确认应用池用户标识是否已获得写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-533">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="f6b7a-534">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-534">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f6b7a-535">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-535">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="f6b7a-536">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-536">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f6b7a-537">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="f6b7a-537">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f6b7a-538">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-538">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f6b7a-539">将 `<handlerSettings>` 元素添加到 `web.config` 中的 `<aspNetCore>` 元素。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-539">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="f6b7a-540">将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-540">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="f6b7a-541">提供给 `<handlerSetting>` 值（上述示例中为 `logs`）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-541">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="f6b7a-542">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\{APP POOL NAME}` 提供写入权限，其中占位符 `{APP POOL NAME}` 为应用池名称）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-542">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="f6b7a-543">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-543">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f6b7a-544">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-544">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f6b7a-545">错误</span><span class="sxs-lookup"><span data-stu-id="f6b7a-545">ERROR</span></span>
* <span data-ttu-id="f6b7a-546">警告</span><span class="sxs-lookup"><span data-stu-id="f6b7a-546">WARNING</span></span>
* <span data-ttu-id="f6b7a-547">INFO</span><span class="sxs-lookup"><span data-stu-id="f6b7a-547">INFO</span></span>
* <span data-ttu-id="f6b7a-548">TRACE</span><span class="sxs-lookup"><span data-stu-id="f6b7a-548">TRACE</span></span>

<span data-ttu-id="f6b7a-549">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-549">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f6b7a-550">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="f6b7a-550">CONSOLE</span></span>
* <span data-ttu-id="f6b7a-551">事件日志</span><span class="sxs-lookup"><span data-stu-id="f6b7a-551">EVENTLOG</span></span>
* <span data-ttu-id="f6b7a-552">文件</span><span class="sxs-lookup"><span data-stu-id="f6b7a-552">FILE</span></span>

<span data-ttu-id="f6b7a-553">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-553">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f6b7a-554">`ASPNETCORE_MODULE_DEBUG_FILE`：调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="f6b7a-555">（默认值：`aspnetcore-debug.log`）</span><span class="sxs-lookup"><span data-stu-id="f6b7a-555">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="f6b7a-556">`ASPNETCORE_MODULE_DEBUG`：调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-556">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f6b7a-557">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-557">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f6b7a-558">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-558">The size of the log isn't limited.</span></span> <span data-ttu-id="f6b7a-559">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-559">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="f6b7a-560">有关 `web.config` 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-560">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f6b7a-561">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="f6b7a-561">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f6b7a-562">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="f6b7a-562">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="f6b7a-563">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-563">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f6b7a-564">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-564">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f6b7a-565">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-565">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f6b7a-566">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-566">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f6b7a-567">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-567">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f6b7a-568">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-568">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f6b7a-569">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-569">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f6b7a-570">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-570">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f6b7a-571">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-571">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f6b7a-572">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-572">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f6b7a-573">ASP.NET Core 模块安装程序使用 `TrustedInstaller` 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-573">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="f6b7a-574">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 `applicationHost.config` 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-574">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="f6b7a-575">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-575">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="f6b7a-576">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-576">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="f6b7a-577">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-577">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f6b7a-578">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-578">Run the installer.</span></span>
1. <span data-ttu-id="f6b7a-579">将已更新的 `applicationHost.config` 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-579">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="f6b7a-580">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-580">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f6b7a-581">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="f6b7a-581">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f6b7a-582">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-582">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f6b7a-583">在托管系统上，导航到 `%windir%\System32\inetsrv`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-583">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="f6b7a-584">找到 `aspnetcore.dll` 文件。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-584">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="f6b7a-585">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-585">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f6b7a-586">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-586">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f6b7a-587">可在 `C:\\Users\\%UserName%\\AppData\\Local\\Temp` 中找到模块的托管捆绑安装程序日志。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-587">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="f6b7a-588">该文件的名称为 `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`，其中占位符 `{TIMESTAMP}` 为时间戳。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-588">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f6b7a-589">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="f6b7a-589">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f6b7a-590">模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-590">Module</span></span>

<span data-ttu-id="f6b7a-591">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-591">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="f6b7a-592">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-592">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="f6b7a-593">架构</span><span class="sxs-lookup"><span data-stu-id="f6b7a-593">Schema</span></span>

<span data-ttu-id="f6b7a-594">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-594">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="f6b7a-595">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-595">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="f6b7a-596">Configuration</span><span class="sxs-lookup"><span data-stu-id="f6b7a-596">Configuration</span></span>

<span data-ttu-id="f6b7a-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-597">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="f6b7a-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-598">**IIS Express**</span></span>

* <span data-ttu-id="f6b7a-599">Visual Studio：`{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="f6b7a-600">iisexpress.exe CLI：`%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="f6b7a-601">通过在 `applicationHost.config` 文件中搜索 `aspnetcore` 可以找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-601">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f6b7a-602">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于将 Web 请求转发到后端 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="f6b7a-603">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-603">Supported Windows versions:</span></span>

* <span data-ttu-id="f6b7a-604">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f6b7a-604">Windows 7 or later</span></span>
* <span data-ttu-id="f6b7a-605">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f6b7a-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f6b7a-606">该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-606">The module only works with Kestrel.</span></span> <span data-ttu-id="f6b7a-607">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="f6b7a-608">由于 ASP.NET Core 应用在独立于 IIS 辅助进程的进程中运行，因此该模块还处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="f6b7a-609">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="f6b7a-610">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的 IIS 中在进程内运行的 ASP.NET 4.x 应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f6b7a-611">下图说明了 IIS、ASP.NET Core 模块和应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模块](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="f6b7a-613">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f6b7a-614">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f6b7a-615">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="f6b7a-616">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="f6b7a-617">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="f6b7a-618">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="f6b7a-619">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f6b7a-620">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f6b7a-621">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="f6b7a-622">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="f6b7a-623">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f6b7a-624">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f6b7a-625">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f6b7a-626">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f6b7a-627">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f6b7a-628">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f6b7a-629">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f6b7a-630">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f6b7a-631">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="f6b7a-631">Configuration with web.config</span></span>

<span data-ttu-id="f6b7a-632">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f6b7a-633">以下 *web.config* 文件发布用于 [依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="f6b7a-634">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="f6b7a-635">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f6b7a-636">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f6b7a-637">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f6b7a-638">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="f6b7a-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f6b7a-639">特性</span><span class="sxs-lookup"><span data-stu-id="f6b7a-639">Attribute</span></span> | <span data-ttu-id="f6b7a-640">描述</span><span class="sxs-lookup"><span data-stu-id="f6b7a-640">Description</span></span> | <span data-ttu-id="f6b7a-641">默认</span><span class="sxs-lookup"><span data-stu-id="f6b7a-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f6b7a-642">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-642">Optional string attribute.</span></span></p><p><span data-ttu-id="f6b7a-643">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f6b7a-644">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f6b7a-645">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f6b7a-646">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f6b7a-647">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f6b7a-648">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="f6b7a-649">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-650">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f6b7a-651">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f6b7a-652">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="f6b7a-653">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-653">Default: `1`</span></span><br><span data-ttu-id="f6b7a-654">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-654">Min: `1`</span></span><br><span data-ttu-id="f6b7a-655">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="f6b7a-656">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-656">Required string attribute.</span></span></p><p><span data-ttu-id="f6b7a-657">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f6b7a-658">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-658">Relative paths are supported.</span></span> <span data-ttu-id="f6b7a-659">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f6b7a-660">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-661">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f6b7a-662">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="f6b7a-663">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-663">Default: `10`</span></span><br><span data-ttu-id="f6b7a-664">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-664">Min: `0`</span></span><br><span data-ttu-id="f6b7a-665">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f6b7a-666">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f6b7a-667">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f6b7a-668">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="f6b7a-669">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-669">Default: `00:02:00`</span></span><br><span data-ttu-id="f6b7a-670">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-670">Min: `00:00:00`</span></span><br><span data-ttu-id="f6b7a-671">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f6b7a-672">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-673">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f6b7a-674">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-674">Default: `10`</span></span><br><span data-ttu-id="f6b7a-675">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-675">Min: `0`</span></span><br><span data-ttu-id="f6b7a-676">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f6b7a-677">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="f6b7a-678">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f6b7a-679">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f6b7a-680">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f6b7a-681">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f6b7a-682">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-682">Default: `120`</span></span><br><span data-ttu-id="f6b7a-683">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-683">Min: `0`</span></span><br><span data-ttu-id="f6b7a-684">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="f6b7a-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f6b7a-685">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f6b7a-686">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f6b7a-687">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-687">Optional string attribute.</span></span></p><p><span data-ttu-id="f6b7a-688">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f6b7a-689">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f6b7a-690">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f6b7a-691">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="f6b7a-692">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f6b7a-693">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="f6b7a-694">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="f6b7a-694">Setting environment variables</span></span>

<span data-ttu-id="f6b7a-695">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f6b7a-696">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="f6b7a-697">本部分中设置的环境变量与使用同一名称设置的系统环境变量冲突。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="f6b7a-698">如果在 web.config 文件以及 Windows 中的系统级别中同时设置了环境变量，则 web.config 文件中的值将被追加到系统环境变量值（例如，`ASPNETCORE_ENVIRONMENT: Development;Development`），这将阻止应用启动。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="f6b7a-699">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-699">The following example sets two environment variables.</span></span> <span data-ttu-id="f6b7a-700">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f6b7a-701">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f6b7a-702">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!WARNING]
> <span data-ttu-id="f6b7a-703">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f6b7a-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="f6b7a-704">app_offline.htm</span></span>

<span data-ttu-id="f6b7a-705">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f6b7a-706">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f6b7a-707">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f6b7a-708">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f6b7a-709">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="f6b7a-709">Start-up error page</span></span>

<span data-ttu-id="f6b7a-710">如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="f6b7a-711">若要禁止显示此页面并还原为默认 IIS 502 状态代码页面，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f6b7a-712">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f6b7a-713">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="f6b7a-713">Log creation and redirection</span></span>

<span data-ttu-id="f6b7a-714">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f6b7a-715">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f6b7a-716">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f6b7a-717">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f6b7a-718">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f6b7a-719">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-719">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="f6b7a-720">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f6b7a-721">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f6b7a-722">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f6b7a-723">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f6b7a-724">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-724">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f6b7a-725">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-725">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="f6b7a-726">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-726">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="f6b7a-727">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-727">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="f6b7a-728">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-728">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f6b7a-729">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-729">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="f6b7a-730">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-730">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="f6b7a-731">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-731">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f6b7a-732">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="f6b7a-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f6b7a-733">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f6b7a-734">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f6b7a-735">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f6b7a-736">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f6b7a-737">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f6b7a-738">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f6b7a-739">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f6b7a-740">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f6b7a-741">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f6b7a-742">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f6b7a-743">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f6b7a-744">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="f6b7a-745">使用 IIS 共享配置时，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="f6b7a-746">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f6b7a-747">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-747">Run the installer.</span></span>
1. <span data-ttu-id="f6b7a-748">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f6b7a-749">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f6b7a-750">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="f6b7a-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f6b7a-751">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f6b7a-752">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f6b7a-753">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f6b7a-754">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f6b7a-755">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f6b7a-756">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f6b7a-757">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="f6b7a-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f6b7a-758">模块</span><span class="sxs-lookup"><span data-stu-id="f6b7a-758">Module</span></span>

<span data-ttu-id="f6b7a-759">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="f6b7a-760">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f6b7a-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f6b7a-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f6b7a-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="f6b7a-762">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="f6b7a-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="f6b7a-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f6b7a-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f6b7a-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f6b7a-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="f6b7a-765">架构</span><span class="sxs-lookup"><span data-stu-id="f6b7a-765">Schema</span></span>

<span data-ttu-id="f6b7a-766">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-766">**IIS**</span></span>

* <span data-ttu-id="f6b7a-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f6b7a-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="f6b7a-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-768">**IIS Express**</span></span>

* <span data-ttu-id="f6b7a-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f6b7a-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="f6b7a-770">Configuration</span><span class="sxs-lookup"><span data-stu-id="f6b7a-770">Configuration</span></span>

<span data-ttu-id="f6b7a-771">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-771">**IIS**</span></span>

* <span data-ttu-id="f6b7a-772">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f6b7a-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f6b7a-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f6b7a-773">**IIS Express**</span></span>

* <span data-ttu-id="f6b7a-774">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f6b7a-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="f6b7a-775">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f6b7a-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f6b7a-776">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f6b7a-777">其他资源</span><span class="sxs-lookup"><span data-stu-id="f6b7a-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="f6b7a-778">[ASP.NET Core 模块引用源[默认分支（主）]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2)：使用“分支”下拉列表选择特定版本（例如，`release/3.1`）。</span><span class="sxs-lookup"><span data-stu-id="f6b7a-778">[ASP.NET Core Module reference source [default branch (master)]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
