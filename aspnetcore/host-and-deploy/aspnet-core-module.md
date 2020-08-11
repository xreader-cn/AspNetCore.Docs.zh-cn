---
title: ASP.NET Core 模块
author: rick-anderson
description: 了解如何配置 ASP.NET Core 模块以托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: e16735ec06e6d84118e5949fe56c514015105242
ms.sourcegitcommit: 6eacadf1be61679ab8e6f781ece76b7395512879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87758575"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="2180d-103">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="2180d-103">ASP.NET Core Module</span></span>

<span data-ttu-id="2180d-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti) 和 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="2180d-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2180d-105">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="2180d-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="2180d-106">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="2180d-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="2180d-107">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="2180d-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="2180d-108">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="2180d-108">Supported Windows versions:</span></span>

* <span data-ttu-id="2180d-109">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2180d-109">Windows 7 or later</span></span>
* <span data-ttu-id="2180d-110">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2180d-110">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="2180d-111">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2180d-112">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2180d-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="2180d-113">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="2180d-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2180d-114">托管模型</span><span class="sxs-lookup"><span data-stu-id="2180d-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="2180d-115">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="2180d-115">In-process hosting model</span></span>

<span data-ttu-id="2180d-116">ASP.NET Core 应用默认为进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="2180d-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="2180d-117">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="2180d-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="2180d-118">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="2180d-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="2180d-119">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2180d-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="2180d-120">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="2180d-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="2180d-121">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="2180d-122">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="2180d-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="2180d-123">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="2180d-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="2180d-124">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="2180d-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="2180d-125">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="2180d-125">Use one app pool per app.</span></span>

* <span data-ttu-id="2180d-126">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="2180d-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2180d-127">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="2180d-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="2180d-128">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="2180d-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="2180d-129">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="2180d-129">Client disconnects are detected.</span></span> <span data-ttu-id="2180d-130">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="2180d-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="2180d-131">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv ）。</span><span class="sxs-lookup"><span data-stu-id="2180d-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="2180d-132">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="2180d-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="2180d-133">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="2180d-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="2180d-134">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="2180d-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="2180d-135">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="2180d-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="2180d-136">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="2180d-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="2180d-137">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="2180d-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="2180d-138">不支持 [Web 包（单文件）部署](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)。</span><span class="sxs-lookup"><span data-stu-id="2180d-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="2180d-139">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="2180d-139">Out-of-process hosting model</span></span>

<span data-ttu-id="2180d-140">若要配置进程外托管应用，请在项目文件 ( *.csproj*) 中将 `<AspNetCoreHostingModel>` 属性的值设置为 `OutOfProcess`：</span><span class="sxs-lookup"><span data-stu-id="2180d-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2180d-141">进程内托管设为 `InProcess`，这是默认值。</span><span class="sxs-lookup"><span data-stu-id="2180d-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="2180d-142">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="2180d-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="2180d-143">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2180d-144">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2180d-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="2180d-145">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="2180d-146">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="2180d-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="2180d-147">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="2180d-147">Hosting model changes</span></span>

<span data-ttu-id="2180d-148">如果 `hostingModel` 设置在 web.config 文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="2180d-149">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="2180d-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="2180d-150">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="2180d-151">进程名</span><span class="sxs-lookup"><span data-stu-id="2180d-151">Process name</span></span>

<span data-ttu-id="2180d-152">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="2180d-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="2180d-153">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="2180d-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2180d-154">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="2180d-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2180d-155">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="2180d-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2180d-156">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2180d-157">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="2180d-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2180d-158">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="2180d-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="2180d-159">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="2180d-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="2180d-160">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="2180d-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="2180d-161">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="2180d-161">Configuration with web.config</span></span>

<span data-ttu-id="2180d-162">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="2180d-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="2180d-163">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="2180d-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="2180d-164">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="2180d-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="2180d-165">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="2180d-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="2180d-166">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="2180d-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2180d-167">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="2180d-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="2180d-168">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="2180d-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="2180d-169">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="2180d-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="2180d-170">特性</span><span class="sxs-lookup"><span data-stu-id="2180d-170">Attribute</span></span> | <span data-ttu-id="2180d-171">描述</span><span class="sxs-lookup"><span data-stu-id="2180d-171">Description</span></span> | <span data-ttu-id="2180d-172">默认</span><span class="sxs-lookup"><span data-stu-id="2180d-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2180d-173">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-173">Optional string attribute.</span></span></p><p><span data-ttu-id="2180d-174">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="2180d-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2180d-175">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2180d-176">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="2180d-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2180d-177">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2180d-178">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2180d-179">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="2180d-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="2180d-180">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-180">Optional string attribute.</span></span></p><p><span data-ttu-id="2180d-181">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="2180d-182">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-183">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="2180d-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2180d-184">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="2180d-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="2180d-185">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="2180d-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2180d-186">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2180d-187">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="2180d-187">Default: `1`</span></span><br><span data-ttu-id="2180d-188">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="2180d-188">Min: `1`</span></span><br><span data-ttu-id="2180d-189">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="2180d-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="2180d-190">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-190">Required string attribute.</span></span></p><p><span data-ttu-id="2180d-191">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2180d-192">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-192">Relative paths are supported.</span></span> <span data-ttu-id="2180d-193">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="2180d-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2180d-194">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-195">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="2180d-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2180d-196">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="2180d-197">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="2180d-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="2180d-198">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="2180d-198">Default: `10`</span></span><br><span data-ttu-id="2180d-199">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="2180d-199">Min: `0`</span></span><br><span data-ttu-id="2180d-200">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="2180d-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2180d-201">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2180d-202">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="2180d-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2180d-203">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="2180d-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="2180d-204">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="2180d-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="2180d-205">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="2180d-206">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="2180d-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="2180d-207">在分钟或秒钟值中使用“60”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="2180d-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="2180d-208">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2180d-208">Default: `00:02:00`</span></span><br><span data-ttu-id="2180d-209">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2180d-209">Min: `00:00:00`</span></span><br><span data-ttu-id="2180d-210">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2180d-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2180d-211">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-212">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="2180d-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2180d-213">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="2180d-213">Default: `10`</span></span><br><span data-ttu-id="2180d-214">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="2180d-214">Min: `0`</span></span><br><span data-ttu-id="2180d-215">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="2180d-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2180d-216">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-217">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="2180d-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2180d-218">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-218">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="2180d-219">进程内托管时：不会重新启动该进程，也不会使用 rapidFailsPerMinute 设置  。</span><span class="sxs-lookup"><span data-stu-id="2180d-219">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="2180d-220">进程外托管时：模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="2180d-220">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2180d-221">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="2180d-221">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2180d-222">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="2180d-222">Default: `120`</span></span><br><span data-ttu-id="2180d-223">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="2180d-223">Min: `0`</span></span><br><span data-ttu-id="2180d-224">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="2180d-224">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2180d-225">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-225">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2180d-226">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="2180d-226">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2180d-227">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-227">Optional string attribute.</span></span></p><p><span data-ttu-id="2180d-228">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-228">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2180d-229">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="2180d-229">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2180d-230">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-230">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2180d-231">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="2180d-231">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="2180d-232">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="2180d-232">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2180d-233">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="2180d-233">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="2180d-234">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="2180d-234">Set environment variables</span></span>

<span data-ttu-id="2180d-235">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-235">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2180d-236">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-236">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="2180d-237">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-237">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="2180d-238">以下示例在 web.config 中设置两个环境变量。`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="2180d-238">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="2180d-239">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="2180d-239">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="2180d-240">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-240">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="2180d-241">直接在 web.config 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="2180d-241">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="2180d-242">此方法在发布项目时设置 web.config 中的环境：</span><span class="sxs-lookup"><span data-stu-id="2180d-242">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="2180d-243">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="2180d-243">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="2180d-244">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="2180d-244">app_offline.htm</span></span>

<span data-ttu-id="2180d-245">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-245">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="2180d-246">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-246">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="2180d-247">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-247">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="2180d-248">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="2180d-248">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="2180d-249">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="2180d-249">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2180d-250">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="2180d-250">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="2180d-251">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="2180d-251">Start-up error page</span></span>

<span data-ttu-id="2180d-252">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="2180d-252">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="2180d-253">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="2180d-253">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="2180d-254">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="2180d-254">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="2180d-255">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="2180d-255">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="2180d-256">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-256">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2180d-257">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="2180d-257">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="2180d-258">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="2180d-258">Log creation and redirection</span></span>

<span data-ttu-id="2180d-259">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="2180d-259">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2180d-260">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="2180d-260">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2180d-261">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="2180d-261">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2180d-262">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-262">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2180d-263">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="2180d-263">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2180d-264">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="2180d-264">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="2180d-265">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="2180d-265">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2180d-266">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="2180d-266">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2180d-267">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="2180d-267">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2180d-268">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="2180d-268">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2180d-269">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="2180d-269">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="2180d-270">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="2180d-270">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="2180d-271">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="2180d-271">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="2180d-272">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="2180d-272">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="2180d-273">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="2180d-273">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="2180d-274">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="2180d-274">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="2180d-275">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="2180d-275">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2180d-276">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-276">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="2180d-277">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="2180d-277">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="2180d-278">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="2180d-278">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="2180d-279">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="2180d-279">Enhanced diagnostic logs</span></span>

<span data-ttu-id="2180d-280">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="2180d-280">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="2180d-281">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="2180d-281">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="2180d-282">创建日志文件时，路径中的任何文件夹（上述示例中为 logs）由模块创建。</span><span class="sxs-lookup"><span data-stu-id="2180d-282">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="2180d-283">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="2180d-283">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2180d-284">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="2180d-284">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="2180d-285">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="2180d-285">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="2180d-286">错误</span><span class="sxs-lookup"><span data-stu-id="2180d-286">ERROR</span></span>
* <span data-ttu-id="2180d-287">警告</span><span class="sxs-lookup"><span data-stu-id="2180d-287">WARNING</span></span>
* <span data-ttu-id="2180d-288">INFO</span><span class="sxs-lookup"><span data-stu-id="2180d-288">INFO</span></span>
* <span data-ttu-id="2180d-289">TRACE</span><span class="sxs-lookup"><span data-stu-id="2180d-289">TRACE</span></span>

<span data-ttu-id="2180d-290">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="2180d-290">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="2180d-291">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="2180d-291">CONSOLE</span></span>
* <span data-ttu-id="2180d-292">事件日志</span><span class="sxs-lookup"><span data-stu-id="2180d-292">EVENTLOG</span></span>
* <span data-ttu-id="2180d-293">文件</span><span class="sxs-lookup"><span data-stu-id="2180d-293">FILE</span></span>

<span data-ttu-id="2180d-294">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="2180d-294">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="2180d-295">`ASPNETCORE_MODULE_DEBUG_FILE`：调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-295">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="2180d-296">（默认值：aspnetcore debug.log）</span><span class="sxs-lookup"><span data-stu-id="2180d-296">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="2180d-297">`ASPNETCORE_MODULE_DEBUG`：调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="2180d-297">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="2180d-298">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="2180d-298">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="2180d-299">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="2180d-299">The size of the log isn't limited.</span></span> <span data-ttu-id="2180d-300">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="2180d-300">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="2180d-301">有关 web.config 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="2180d-301">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="2180d-302">修改堆栈大小</span><span class="sxs-lookup"><span data-stu-id="2180d-302">Modify the stack size</span></span>

<span data-ttu-id="2180d-303">*仅适用于使用进程内托管模型的情况。*</span><span class="sxs-lookup"><span data-stu-id="2180d-303">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="2180d-304">使用 web.config 中的 `stackSize` 设置（以字节为单位）配置托管堆栈大小。默认大小为 `1048576` 个字节 (1 MB)。</span><span class="sxs-lookup"><span data-stu-id="2180d-304">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="2180d-305">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="2180d-305">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="2180d-306">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="2180d-306">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="2180d-307">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="2180d-307">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="2180d-308">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="2180d-308">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="2180d-309">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="2180d-309">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="2180d-310">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-310">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="2180d-311">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-311">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="2180d-312">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="2180d-312">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="2180d-313">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-313">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="2180d-314">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="2180d-314">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="2180d-315">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-315">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="2180d-316">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="2180d-316">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="2180d-317">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="2180d-317">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="2180d-318">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="2180d-318">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="2180d-319">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="2180d-319">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="2180d-320">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="2180d-320">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="2180d-321">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="2180d-321">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2180d-322">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="2180d-322">Run the installer.</span></span>
1. <span data-ttu-id="2180d-323">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="2180d-323">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2180d-324">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="2180d-324">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2180d-325">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="2180d-325">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2180d-326">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2180d-326">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2180d-327">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="2180d-327">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="2180d-328">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="2180d-328">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="2180d-329">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="2180d-329">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2180d-330">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="2180d-330">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2180d-331">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="2180d-331">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="2180d-332">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="2180d-332">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="2180d-333">模块</span><span class="sxs-lookup"><span data-stu-id="2180d-333">Module</span></span>

<span data-ttu-id="2180d-334">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="2180d-334">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="2180d-335">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-335">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2180d-336">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-336">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2180d-337">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-337">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2180d-338">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-338">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="2180d-339">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="2180d-339">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="2180d-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-340">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2180d-341">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-341">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2180d-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-342">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2180d-343">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-343">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="2180d-344">架构</span><span class="sxs-lookup"><span data-stu-id="2180d-344">Schema</span></span>

<span data-ttu-id="2180d-345">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2180d-345">**IIS**</span></span>

* <span data-ttu-id="2180d-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2180d-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2180d-347">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2180d-347">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="2180d-348">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2180d-348">**IIS Express**</span></span>

* <span data-ttu-id="2180d-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2180d-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2180d-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2180d-350">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="2180d-351">Configuration</span><span class="sxs-lookup"><span data-stu-id="2180d-351">Configuration</span></span>

<span data-ttu-id="2180d-352">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2180d-352">**IIS**</span></span>

* <span data-ttu-id="2180d-353">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2180d-353">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="2180d-354">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2180d-354">**IIS Express**</span></span>

* <span data-ttu-id="2180d-355">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2180d-355">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="2180d-356">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="2180d-356">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="2180d-357">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="2180d-357">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2180d-358">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="2180d-358">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="2180d-359">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="2180d-359">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="2180d-360">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="2180d-360">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="2180d-361">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="2180d-361">Supported Windows versions:</span></span>

* <span data-ttu-id="2180d-362">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2180d-362">Windows 7 or later</span></span>
* <span data-ttu-id="2180d-363">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2180d-363">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2180d-364">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-364">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2180d-365">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2180d-365">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="2180d-366">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="2180d-366">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2180d-367">托管模型</span><span class="sxs-lookup"><span data-stu-id="2180d-367">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="2180d-368">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="2180d-368">In-process hosting model</span></span>

<span data-ttu-id="2180d-369">若要配置用于进程内托管的应用，请将 `<AspNetCoreHostingModel>` 属性添加到值为 `InProcess`（进程外托管使用 `OutOfProcess` 进行设置）的应用项目文件：</span><span class="sxs-lookup"><span data-stu-id="2180d-369">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2180d-370">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="2180d-370">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="2180d-371">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="2180d-371">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="2180d-372">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="2180d-372">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="2180d-373">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="2180d-373">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="2180d-374">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="2180d-374">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="2180d-375">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2180d-375">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="2180d-376">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="2180d-376">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="2180d-377">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-377">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="2180d-378">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="2180d-378">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="2180d-379">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="2180d-379">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="2180d-380">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="2180d-380">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="2180d-381">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="2180d-381">Use one app pool per app.</span></span>

* <span data-ttu-id="2180d-382">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="2180d-382">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2180d-383">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="2180d-383">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="2180d-384">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="2180d-384">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="2180d-385">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="2180d-385">Client disconnects are detected.</span></span> <span data-ttu-id="2180d-386">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="2180d-386">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="2180d-387">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv ）。</span><span class="sxs-lookup"><span data-stu-id="2180d-387">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="2180d-388">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="2180d-388">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="2180d-389">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="2180d-389">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="2180d-390">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="2180d-390">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="2180d-391">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="2180d-391">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="2180d-392">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="2180d-392">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="2180d-393">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="2180d-393">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="2180d-394">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="2180d-394">Out-of-process hosting model</span></span>

<span data-ttu-id="2180d-395">若要配置进程外托管应用，请在项目文件中使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="2180d-395">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="2180d-396">不指定 `<AspNetCoreHostingModel>` 属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-396">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="2180d-397">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="2180d-397">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="2180d-398">将 `<AspNetCoreHostingModel>` 属性的值设为 `OutOfProcess`（进程内托管设为 `InProcess`）：</span><span class="sxs-lookup"><span data-stu-id="2180d-398">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2180d-399">值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="2180d-399">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="2180d-400">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-400">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2180d-401">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2180d-401">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="2180d-402">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-402">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="2180d-403">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="2180d-403">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="2180d-404">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="2180d-404">Hosting model changes</span></span>

<span data-ttu-id="2180d-405">如果 `hostingModel` 设置在 web.config 文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-405">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="2180d-406">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="2180d-406">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="2180d-407">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-407">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="2180d-408">进程名</span><span class="sxs-lookup"><span data-stu-id="2180d-408">Process name</span></span>

<span data-ttu-id="2180d-409">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="2180d-409">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="2180d-410">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="2180d-410">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2180d-411">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="2180d-411">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2180d-412">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="2180d-412">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2180d-413">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-413">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2180d-414">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="2180d-414">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2180d-415">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="2180d-415">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="2180d-416">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="2180d-416">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="2180d-417">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="2180d-417">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="2180d-418">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="2180d-418">Configuration with web.config</span></span>

<span data-ttu-id="2180d-419">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="2180d-419">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="2180d-420">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="2180d-420">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="2180d-421">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="2180d-421">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="2180d-422">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="2180d-422">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="2180d-423">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="2180d-423">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2180d-424">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="2180d-424">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="2180d-425">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="2180d-425">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="2180d-426">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="2180d-426">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="2180d-427">特性</span><span class="sxs-lookup"><span data-stu-id="2180d-427">Attribute</span></span> | <span data-ttu-id="2180d-428">说明</span><span class="sxs-lookup"><span data-stu-id="2180d-428">Description</span></span> | <span data-ttu-id="2180d-429">默认</span><span class="sxs-lookup"><span data-stu-id="2180d-429">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2180d-430">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-430">Optional string attribute.</span></span></p><p><span data-ttu-id="2180d-431">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="2180d-431">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2180d-432">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-432">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2180d-433">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="2180d-433">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2180d-434">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-434">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2180d-435">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-435">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2180d-436">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="2180d-436">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="2180d-437">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-437">Optional string attribute.</span></span></p><p><span data-ttu-id="2180d-438">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-438">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="2180d-439">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-439">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-440">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="2180d-440">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2180d-441">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="2180d-441">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="2180d-442">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="2180d-442">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2180d-443">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-443">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2180d-444">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="2180d-444">Default: `1`</span></span><br><span data-ttu-id="2180d-445">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="2180d-445">Min: `1`</span></span><br><span data-ttu-id="2180d-446">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="2180d-446">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="2180d-447">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-447">Required string attribute.</span></span></p><p><span data-ttu-id="2180d-448">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-448">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2180d-449">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-449">Relative paths are supported.</span></span> <span data-ttu-id="2180d-450">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="2180d-450">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2180d-451">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-451">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-452">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="2180d-452">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2180d-453">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-453">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="2180d-454">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="2180d-454">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="2180d-455">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="2180d-455">Default: `10`</span></span><br><span data-ttu-id="2180d-456">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="2180d-456">Min: `0`</span></span><br><span data-ttu-id="2180d-457">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="2180d-457">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2180d-458">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-458">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2180d-459">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="2180d-459">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2180d-460">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="2180d-460">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="2180d-461">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="2180d-461">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="2180d-462">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-462">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="2180d-463">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="2180d-463">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="2180d-464">在分钟或秒钟值中使用“60”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="2180d-464">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="2180d-465">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2180d-465">Default: `00:02:00`</span></span><br><span data-ttu-id="2180d-466">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2180d-466">Min: `00:00:00`</span></span><br><span data-ttu-id="2180d-467">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2180d-467">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2180d-468">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-468">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-469">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="2180d-469">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2180d-470">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="2180d-470">Default: `10`</span></span><br><span data-ttu-id="2180d-471">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="2180d-471">Min: `0`</span></span><br><span data-ttu-id="2180d-472">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="2180d-472">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2180d-473">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-473">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-474">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="2180d-474">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2180d-475">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-475">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="2180d-476">进程内托管时：不会重新启动该进程，也不会使用 rapidFailsPerMinute 设置  。</span><span class="sxs-lookup"><span data-stu-id="2180d-476">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="2180d-477">进程外托管时：模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="2180d-477">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2180d-478">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="2180d-478">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2180d-479">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="2180d-479">Default: `120`</span></span><br><span data-ttu-id="2180d-480">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="2180d-480">Min: `0`</span></span><br><span data-ttu-id="2180d-481">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="2180d-481">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2180d-482">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-482">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2180d-483">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="2180d-483">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2180d-484">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-484">Optional string attribute.</span></span></p><p><span data-ttu-id="2180d-485">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-485">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2180d-486">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="2180d-486">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2180d-487">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-487">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2180d-488">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="2180d-488">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="2180d-489">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="2180d-489">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2180d-490">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="2180d-490">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="2180d-491">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="2180d-491">Setting environment variables</span></span>

<span data-ttu-id="2180d-492">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-492">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2180d-493">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-493">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="2180d-494">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-494">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="2180d-495">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-495">The following example sets two environment variables.</span></span> <span data-ttu-id="2180d-496">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="2180d-496">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="2180d-497">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="2180d-497">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="2180d-498">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-498">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="2180d-499">直接在 web.config 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="2180d-499">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="2180d-500">此方法在发布项目时设置 web.config 中的环境：</span><span class="sxs-lookup"><span data-stu-id="2180d-500">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="2180d-501">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="2180d-501">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="2180d-502">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="2180d-502">app_offline.htm</span></span>

<span data-ttu-id="2180d-503">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-503">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="2180d-504">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-504">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="2180d-505">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-505">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="2180d-506">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="2180d-506">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="2180d-507">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="2180d-507">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2180d-508">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="2180d-508">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="2180d-509">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="2180d-509">Start-up error page</span></span>

<span data-ttu-id="2180d-510">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="2180d-510">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="2180d-511">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="2180d-511">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="2180d-512">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="2180d-512">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="2180d-513">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="2180d-513">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="2180d-514">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-514">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2180d-515">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="2180d-515">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="2180d-516">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="2180d-516">Log creation and redirection</span></span>

<span data-ttu-id="2180d-517">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="2180d-517">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2180d-518">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="2180d-518">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2180d-519">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="2180d-519">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2180d-520">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-520">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2180d-521">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="2180d-521">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2180d-522">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="2180d-522">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="2180d-523">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="2180d-523">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2180d-524">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="2180d-524">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2180d-525">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="2180d-525">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2180d-526">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="2180d-526">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2180d-527">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="2180d-527">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="2180d-528">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="2180d-528">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="2180d-529">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="2180d-529">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="2180d-530">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="2180d-530">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="2180d-531">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="2180d-531">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="2180d-532">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="2180d-532">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="2180d-533">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="2180d-533">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2180d-534">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-534">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="2180d-535">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="2180d-535">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="2180d-536">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="2180d-536">Enhanced diagnostic logs</span></span>

<span data-ttu-id="2180d-537">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="2180d-537">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="2180d-538">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="2180d-538">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="2180d-539">提供给 `<handlerSetting>` 值（上述示例中为 logs）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="2180d-539">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="2180d-540">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="2180d-540">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2180d-541">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="2180d-541">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="2180d-542">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="2180d-542">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="2180d-543">错误</span><span class="sxs-lookup"><span data-stu-id="2180d-543">ERROR</span></span>
* <span data-ttu-id="2180d-544">警告</span><span class="sxs-lookup"><span data-stu-id="2180d-544">WARNING</span></span>
* <span data-ttu-id="2180d-545">INFO</span><span class="sxs-lookup"><span data-stu-id="2180d-545">INFO</span></span>
* <span data-ttu-id="2180d-546">TRACE</span><span class="sxs-lookup"><span data-stu-id="2180d-546">TRACE</span></span>

<span data-ttu-id="2180d-547">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="2180d-547">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="2180d-548">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="2180d-548">CONSOLE</span></span>
* <span data-ttu-id="2180d-549">事件日志</span><span class="sxs-lookup"><span data-stu-id="2180d-549">EVENTLOG</span></span>
* <span data-ttu-id="2180d-550">文件</span><span class="sxs-lookup"><span data-stu-id="2180d-550">FILE</span></span>

<span data-ttu-id="2180d-551">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="2180d-551">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="2180d-552">`ASPNETCORE_MODULE_DEBUG_FILE`：调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-552">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="2180d-553">（默认值：aspnetcore debug.log）</span><span class="sxs-lookup"><span data-stu-id="2180d-553">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="2180d-554">`ASPNETCORE_MODULE_DEBUG`：调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="2180d-554">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="2180d-555">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="2180d-555">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="2180d-556">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="2180d-556">The size of the log isn't limited.</span></span> <span data-ttu-id="2180d-557">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="2180d-557">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="2180d-558">有关 web.config 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="2180d-558">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="2180d-559">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="2180d-559">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="2180d-560">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="2180d-560">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="2180d-561">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="2180d-561">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="2180d-562">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="2180d-562">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="2180d-563">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="2180d-563">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="2180d-564">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-564">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="2180d-565">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-565">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="2180d-566">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="2180d-566">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="2180d-567">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-567">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="2180d-568">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="2180d-568">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="2180d-569">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-569">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="2180d-570">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="2180d-570">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="2180d-571">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="2180d-571">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="2180d-572">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="2180d-572">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="2180d-573">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="2180d-573">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="2180d-574">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="2180d-574">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="2180d-575">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="2180d-575">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2180d-576">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="2180d-576">Run the installer.</span></span>
1. <span data-ttu-id="2180d-577">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="2180d-577">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2180d-578">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="2180d-578">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2180d-579">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="2180d-579">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2180d-580">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2180d-580">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2180d-581">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="2180d-581">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="2180d-582">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="2180d-582">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="2180d-583">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="2180d-583">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2180d-584">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="2180d-584">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2180d-585">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="2180d-585">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="2180d-586">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="2180d-586">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="2180d-587">模块</span><span class="sxs-lookup"><span data-stu-id="2180d-587">Module</span></span>

<span data-ttu-id="2180d-588">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="2180d-588">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="2180d-589">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-589">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2180d-590">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-590">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2180d-591">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-591">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2180d-592">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-592">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="2180d-593">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="2180d-593">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="2180d-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-594">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2180d-595">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-595">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2180d-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-596">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2180d-597">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-597">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="2180d-598">架构</span><span class="sxs-lookup"><span data-stu-id="2180d-598">Schema</span></span>

<span data-ttu-id="2180d-599">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2180d-599">**IIS**</span></span>

* <span data-ttu-id="2180d-600">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2180d-600">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2180d-601">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2180d-601">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="2180d-602">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2180d-602">**IIS Express**</span></span>

* <span data-ttu-id="2180d-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2180d-603">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="2180d-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2180d-604">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="2180d-605">Configuration</span><span class="sxs-lookup"><span data-stu-id="2180d-605">Configuration</span></span>

<span data-ttu-id="2180d-606">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2180d-606">**IIS**</span></span>

* <span data-ttu-id="2180d-607">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2180d-607">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="2180d-608">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2180d-608">**IIS Express**</span></span>

* <span data-ttu-id="2180d-609">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2180d-609">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="2180d-610">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="2180d-610">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="2180d-611">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="2180d-611">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2180d-612">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于将 Web 请求转发到后端 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="2180d-612">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="2180d-613">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="2180d-613">Supported Windows versions:</span></span>

* <span data-ttu-id="2180d-614">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2180d-614">Windows 7 or later</span></span>
* <span data-ttu-id="2180d-615">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2180d-615">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2180d-616">该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2180d-616">The module only works with Kestrel.</span></span> <span data-ttu-id="2180d-617">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="2180d-617">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="2180d-618">由于 ASP.NET Core 应用在独立于 IIS 辅助进程的进程中运行，因此该模块还处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="2180d-618">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="2180d-619">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="2180d-619">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="2180d-620">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的 IIS 中在进程内运行的 ASP.NET 4.x 应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="2180d-620">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2180d-621">下图说明了 IIS、ASP.NET Core 模块和应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="2180d-621">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模块](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="2180d-623">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="2180d-623">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="2180d-624">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="2180d-624">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="2180d-625">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2180d-625">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="2180d-626">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="2180d-626">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="2180d-627">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-627">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="2180d-628">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="2180d-628">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="2180d-629">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="2180d-629">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="2180d-630">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="2180d-630">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="2180d-631">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2180d-631">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="2180d-632">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="2180d-632">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="2180d-633">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="2180d-633">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2180d-634">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="2180d-634">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2180d-635">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="2180d-635">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2180d-636">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-636">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2180d-637">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="2180d-637">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2180d-638">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="2180d-638">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="2180d-639">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="2180d-639">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="2180d-640">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="2180d-640">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="2180d-641">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="2180d-641">Configuration with web.config</span></span>

<span data-ttu-id="2180d-642">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="2180d-642">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="2180d-643">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="2180d-643">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="2180d-644">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="2180d-644">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="2180d-645">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="2180d-645">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2180d-646">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="2180d-646">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="2180d-647">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="2180d-647">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="2180d-648">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="2180d-648">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="2180d-649">特性</span><span class="sxs-lookup"><span data-stu-id="2180d-649">Attribute</span></span> | <span data-ttu-id="2180d-650">说明</span><span class="sxs-lookup"><span data-stu-id="2180d-650">Description</span></span> | <span data-ttu-id="2180d-651">默认</span><span class="sxs-lookup"><span data-stu-id="2180d-651">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2180d-652">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-652">Optional string attribute.</span></span></p><p><span data-ttu-id="2180d-653">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="2180d-653">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2180d-654">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2180d-655">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="2180d-655">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2180d-656">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-656">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2180d-657">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-657">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2180d-658">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="2180d-658">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="2180d-659">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-659">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-660">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="2180d-660">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2180d-661">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="2180d-661">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2180d-662">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-662">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2180d-663">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="2180d-663">Default: `1`</span></span><br><span data-ttu-id="2180d-664">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="2180d-664">Min: `1`</span></span><br><span data-ttu-id="2180d-665">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="2180d-665">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="2180d-666">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-666">Required string attribute.</span></span></p><p><span data-ttu-id="2180d-667">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-667">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2180d-668">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-668">Relative paths are supported.</span></span> <span data-ttu-id="2180d-669">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="2180d-669">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2180d-670">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-670">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-671">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="2180d-671">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2180d-672">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-672">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="2180d-673">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="2180d-673">Default: `10`</span></span><br><span data-ttu-id="2180d-674">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="2180d-674">Min: `0`</span></span><br><span data-ttu-id="2180d-675">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="2180d-675">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2180d-676">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-676">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2180d-677">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="2180d-677">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2180d-678">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="2180d-678">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="2180d-679">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2180d-679">Default: `00:02:00`</span></span><br><span data-ttu-id="2180d-680">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2180d-680">Min: `00:00:00`</span></span><br><span data-ttu-id="2180d-681">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2180d-681">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2180d-682">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-682">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-683">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="2180d-683">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2180d-684">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="2180d-684">Default: `10`</span></span><br><span data-ttu-id="2180d-685">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="2180d-685">Min: `0`</span></span><br><span data-ttu-id="2180d-686">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="2180d-686">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2180d-687">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-687">Optional integer attribute.</span></span></p><p><span data-ttu-id="2180d-688">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="2180d-688">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2180d-689">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-689">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="2180d-690">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="2180d-690">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2180d-691">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="2180d-691">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2180d-692">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="2180d-692">Default: `120`</span></span><br><span data-ttu-id="2180d-693">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="2180d-693">Min: `0`</span></span><br><span data-ttu-id="2180d-694">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="2180d-694">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2180d-695">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-695">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2180d-696">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="2180d-696">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2180d-697">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-697">Optional string attribute.</span></span></p><p><span data-ttu-id="2180d-698">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-698">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2180d-699">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="2180d-699">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2180d-700">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-700">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2180d-701">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="2180d-701">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="2180d-702">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="2180d-702">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2180d-703">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="2180d-703">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="2180d-704">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="2180d-704">Setting environment variables</span></span>

<span data-ttu-id="2180d-705">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-705">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2180d-706">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-706">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="2180d-707">本部分中设置的环境变量与使用同一名称设置的系统环境变量冲突。</span><span class="sxs-lookup"><span data-stu-id="2180d-707">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="2180d-708">如果在 web.config 文件以及 Windows 中的系统级别中同时设置了环境变量，则 web.config 文件中的值将被追加到系统环境变量值（例如，`ASPNETCORE_ENVIRONMENT: Development;Development`），这将阻止应用启动。</span><span class="sxs-lookup"><span data-stu-id="2180d-708">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="2180d-709">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-709">The following example sets two environment variables.</span></span> <span data-ttu-id="2180d-710">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="2180d-710">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="2180d-711">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="2180d-711">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="2180d-712">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="2180d-712">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="2180d-713">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="2180d-713">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="2180d-714">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="2180d-714">app_offline.htm</span></span>

<span data-ttu-id="2180d-715">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-715">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="2180d-716">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-716">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="2180d-717">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-717">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="2180d-718">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="2180d-718">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="2180d-719">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="2180d-719">Start-up error page</span></span>

<span data-ttu-id="2180d-720">如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="2180d-720">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="2180d-721">若要禁止显示此页面并还原为默认 IIS 502 状态代码页面，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="2180d-721">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2180d-722">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="2180d-722">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![“502.5 进程失败”状态代码页面](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="2180d-724">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="2180d-724">Log creation and redirection</span></span>

<span data-ttu-id="2180d-725">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="2180d-725">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2180d-726">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="2180d-726">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2180d-727">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="2180d-727">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2180d-728">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="2180d-728">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2180d-729">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="2180d-729">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2180d-730">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="2180d-730">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="2180d-731">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="2180d-731">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2180d-732">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="2180d-732">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2180d-733">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="2180d-733">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2180d-734">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="2180d-734">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2180d-735">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="2180d-735">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="2180d-736">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="2180d-736">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="2180d-737">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="2180d-737">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="2180d-738">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="2180d-738">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="2180d-739">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="2180d-739">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2180d-740">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="2180d-740">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="2180d-741">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="2180d-741">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="2180d-742">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="2180d-742">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="2180d-743">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="2180d-743">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="2180d-744">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="2180d-744">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="2180d-745">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="2180d-745">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="2180d-746">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="2180d-746">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="2180d-747">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-747">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="2180d-748">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="2180d-748">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="2180d-749">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="2180d-749">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="2180d-750">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-750">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="2180d-751">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="2180d-751">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="2180d-752">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="2180d-752">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="2180d-753">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="2180d-753">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="2180d-754">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="2180d-754">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="2180d-755">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="2180d-755">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="2180d-756">使用 IIS 共享配置时，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2180d-756">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="2180d-757">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="2180d-757">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2180d-758">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="2180d-758">Run the installer.</span></span>
1. <span data-ttu-id="2180d-759">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="2180d-759">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2180d-760">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="2180d-760">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2180d-761">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="2180d-761">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2180d-762">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2180d-762">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2180d-763">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="2180d-763">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="2180d-764">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="2180d-764">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="2180d-765">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="2180d-765">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2180d-766">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="2180d-766">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2180d-767">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="2180d-767">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="2180d-768">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="2180d-768">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="2180d-769">模块</span><span class="sxs-lookup"><span data-stu-id="2180d-769">Module</span></span>

<span data-ttu-id="2180d-770">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="2180d-770">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="2180d-771">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-771">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2180d-772">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-772">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="2180d-773">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="2180d-773">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="2180d-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-774">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2180d-775">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2180d-775">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="2180d-776">架构</span><span class="sxs-lookup"><span data-stu-id="2180d-776">Schema</span></span>

<span data-ttu-id="2180d-777">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2180d-777">**IIS**</span></span>

* <span data-ttu-id="2180d-778">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2180d-778">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="2180d-779">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2180d-779">**IIS Express**</span></span>

* <span data-ttu-id="2180d-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2180d-780">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="2180d-781">Configuration</span><span class="sxs-lookup"><span data-stu-id="2180d-781">Configuration</span></span>

<span data-ttu-id="2180d-782">**IIS**</span><span class="sxs-lookup"><span data-stu-id="2180d-782">**IIS**</span></span>

* <span data-ttu-id="2180d-783">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2180d-783">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="2180d-784">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2180d-784">**IIS Express**</span></span>

* <span data-ttu-id="2180d-785">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2180d-785">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="2180d-786">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="2180d-786">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="2180d-787">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="2180d-787">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2180d-788">其他资源</span><span class="sxs-lookup"><span data-stu-id="2180d-788">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="2180d-789">[ASP.NET Core 模块引用源（主分支）](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2)：使用“分支”下拉列表选择特定版本（例如，`release/3.1`）。</span><span class="sxs-lookup"><span data-stu-id="2180d-789">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
