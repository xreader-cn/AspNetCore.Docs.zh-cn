---
title: ASP.NET Core 模块
author: guardrex
description: 了解如何配置 ASP.NET Core 模块以托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 917ee462a8f9592120685b53d059a661cb4a7452
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333893"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="68281-103">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="68281-103">ASP.NET Core Module</span></span>

<span data-ttu-id="68281-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="68281-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68281-105">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="68281-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="68281-106">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="68281-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="68281-107">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="68281-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="68281-108">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="68281-108">Supported Windows versions:</span></span>

* <span data-ttu-id="68281-109">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="68281-109">Windows 7 or later</span></span>
* <span data-ttu-id="68281-110">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="68281-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="68281-111">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="68281-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="68281-112">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="68281-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="68281-113">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="68281-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="68281-114">托管模型</span><span class="sxs-lookup"><span data-stu-id="68281-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="68281-115">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="68281-115">In-process hosting model</span></span>

<span data-ttu-id="68281-116">ASP.NET Core 应用默认为进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="68281-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="68281-117">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="68281-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="68281-118">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="68281-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="68281-119">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="68281-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="68281-120">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="68281-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="68281-121">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="68281-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="68281-122">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="68281-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="68281-123">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="68281-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="68281-124">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="68281-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="68281-125">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="68281-125">Use one app pool per app.</span></span>

* <span data-ttu-id="68281-126">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="68281-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="68281-127">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="68281-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="68281-128">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="68281-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="68281-129">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="68281-129">Client disconnects are detected.</span></span> <span data-ttu-id="68281-130">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="68281-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="68281-131">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv   ）。</span><span class="sxs-lookup"><span data-stu-id="68281-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="68281-132">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="68281-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="68281-133">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="68281-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="68281-134">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="68281-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="68281-135">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="68281-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="68281-136">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="68281-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="68281-137">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="68281-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="68281-138">不支持 [Web 包（单文件）部署](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)。</span><span class="sxs-lookup"><span data-stu-id="68281-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="68281-139">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="68281-139">Out-of-process hosting model</span></span>

<span data-ttu-id="68281-140">若要配置进程外托管应用，请在项目文件 ( *.csproj*) 中将 `<AspNetCoreHostingModel>` 属性的值设置为 `OutOfProcess`：</span><span class="sxs-lookup"><span data-stu-id="68281-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="68281-141">进程内托管设为 `InProcess`，这是默认值。</span><span class="sxs-lookup"><span data-stu-id="68281-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="68281-142">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="68281-142">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="68281-143">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="68281-143">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="68281-144">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="68281-144">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="68281-145">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="68281-145">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="68281-146">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="68281-146">Hosting model changes</span></span>

<span data-ttu-id="68281-147">如果 `hostingModel` 设置在 web.config  文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="68281-147">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="68281-148">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="68281-148">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="68281-149">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="68281-149">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="68281-150">进程名</span><span class="sxs-lookup"><span data-stu-id="68281-150">Process name</span></span>

<span data-ttu-id="68281-151">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="68281-151">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="68281-152">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="68281-152">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="68281-153">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="68281-153">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="68281-154">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="68281-154">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="68281-155">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-155">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="68281-156">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="68281-156">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="68281-157">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="68281-157">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="68281-158">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="68281-158">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="68281-159">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="68281-159">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="68281-160">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="68281-160">Configuration with web.config</span></span>

<span data-ttu-id="68281-161">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="68281-161">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="68281-162">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="68281-162">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="68281-163">以下 web.config  发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="68281-163">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="68281-164">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="68281-164">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="68281-165">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="68281-165">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="68281-166">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="68281-166">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="68281-167">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="68281-167">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="68281-168">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="68281-168">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="68281-169">特性</span><span class="sxs-lookup"><span data-stu-id="68281-169">Attribute</span></span> | <span data-ttu-id="68281-170">说明</span><span class="sxs-lookup"><span data-stu-id="68281-170">Description</span></span> | <span data-ttu-id="68281-171">默认</span><span class="sxs-lookup"><span data-stu-id="68281-171">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="68281-172">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-172">Optional string attribute.</span></span></p><p><span data-ttu-id="68281-173">processPath  中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="68281-173">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="68281-174">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="68281-174">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="68281-175">如果为 true，将禁止显示“502.5 - 进程失败”  页面，而会优先显示 web.config  中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="68281-175">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="68281-176">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="68281-176">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="68281-177">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="68281-177">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="68281-178">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="68281-178">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="68281-179">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-179">Optional string attribute.</span></span></p><p><span data-ttu-id="68281-180">将托管模型指定为进程内 (`InProcess`) 或进程外 (`OutOfProcess`)。</span><span class="sxs-lookup"><span data-stu-id="68281-180">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `InProcess` |
| `processesPerApplication` | <p><span data-ttu-id="68281-181">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-181">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-182">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="68281-182">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="68281-183">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="68281-183">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="68281-184">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="68281-184">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="68281-185">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="68281-185">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="68281-186">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="68281-186">Default: `1`</span></span><br><span data-ttu-id="68281-187">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="68281-187">Min: `1`</span></span><br><span data-ttu-id="68281-188">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="68281-188">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="68281-189">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-189">Required string attribute.</span></span></p><p><span data-ttu-id="68281-190">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="68281-190">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="68281-191">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="68281-191">Relative paths are supported.</span></span> <span data-ttu-id="68281-192">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="68281-192">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="68281-193">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-194">指定允许 processPath  中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="68281-194">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="68281-195">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="68281-195">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="68281-196">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="68281-196">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="68281-197">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="68281-197">Default: `10`</span></span><br><span data-ttu-id="68281-198">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="68281-198">Min: `0`</span></span><br><span data-ttu-id="68281-199">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="68281-199">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="68281-200">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="68281-200">Optional timespan attribute.</span></span></p><p><span data-ttu-id="68281-201">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="68281-201">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="68281-202">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="68281-202">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="68281-203">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="68281-203">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="68281-204">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="68281-204">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="68281-205">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="68281-205">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="68281-206">在分钟或秒钟值中使用“60”  会导致“500 - 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="68281-206">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="68281-207">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="68281-207">Default: `00:02:00`</span></span><br><span data-ttu-id="68281-208">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="68281-208">Min: `00:00:00`</span></span><br><span data-ttu-id="68281-209">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="68281-209">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="68281-210">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-210">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-211">检测到 app_offline.htm  文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="68281-211">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="68281-212">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="68281-212">Default: `10`</span></span><br><span data-ttu-id="68281-213">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="68281-213">Min: `0`</span></span><br><span data-ttu-id="68281-214">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="68281-214">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="68281-215">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-215">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-216">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="68281-216">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="68281-217">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="68281-217">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="68281-218">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute  次。</span><span class="sxs-lookup"><span data-stu-id="68281-218">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="68281-219">值 0（零）  不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="68281-219">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="68281-220">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="68281-220">Default: `120`</span></span><br><span data-ttu-id="68281-221">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="68281-221">Min: `0`</span></span><br><span data-ttu-id="68281-222">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="68281-222">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="68281-223">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="68281-223">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="68281-224">如果为 true，processPath  中指定的 进程的 stdout  和 stderr  将重定向到 stdoutLogFile  中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="68281-224">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="68281-225">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-225">Optional string attribute.</span></span></p><p><span data-ttu-id="68281-226">指定在其中记录 processPath  中指定进程的 stdout  和 stderr  的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="68281-226">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="68281-227">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="68281-227">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="68281-228">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="68281-228">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="68281-229">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="68281-229">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="68281-230">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log  ) 添加到 stdoutLogFile  路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="68281-230">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="68281-231">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs  文件夹中保存为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="68281-231">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="68281-232">设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-232">Set environment variables</span></span>

<span data-ttu-id="68281-233">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-233">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="68281-234">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-234">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="68281-235">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-235">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="68281-236">以下示例在 web.config  中设置两个环境变量。`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="68281-236">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="68281-237">开发人员可能会暂时在 web.config  文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="68281-237">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="68281-238">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="68281-238">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!NOTE]
> <span data-ttu-id="68281-239">直接在 web.config  中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml  ）或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="68281-239">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="68281-240">此方法在发布项目时设置 web.config  中的环境：</span><span class="sxs-lookup"><span data-stu-id="68281-240">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="68281-241">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="68281-241">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="68281-242">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="68281-242">app_offline.htm</span></span>

<span data-ttu-id="68281-243">如果在应用的根目录中检测到名为 “app_offline.htm”  的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="68281-243">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="68281-244">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="68281-244">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="68281-245">存在 app_offline.htm  文件时，ASP.NET Core 模块会通过发送回 app_offline.htm  文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="68281-245">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="68281-246">删除 app_offline.htm  文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="68281-246">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="68281-247">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="68281-247">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="68281-248">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="68281-248">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="68281-249">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="68281-249">Start-up error page</span></span>

<span data-ttu-id="68281-250">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="68281-250">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="68281-251">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="68281-251">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="68281-252">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="68281-252">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="68281-253">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”  状态代码页。</span><span class="sxs-lookup"><span data-stu-id="68281-253">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="68281-254">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="68281-254">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="68281-255">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="68281-255">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="68281-256">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="68281-256">Log creation and redirection</span></span>

<span data-ttu-id="68281-257">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="68281-257">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="68281-258">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="68281-258">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="68281-259">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="68281-259">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="68281-260">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="68281-260">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="68281-261">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="68281-261">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="68281-262">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="68281-262">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="68281-263">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="68281-263">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="68281-264">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="68281-264">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="68281-265">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="68281-265">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="68281-266">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="68281-266">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="68281-267">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log  ) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout  ）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="68281-267">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="68281-268">如果 `stdoutLogFile` 路径以 stdout  结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="68281-268">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="68281-269">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="68281-269">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="68281-270">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="68281-270">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="68281-271">web.config 文件中的以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录  。</span><span class="sxs-lookup"><span data-stu-id="68281-271">The following sample `aspNetCore` element in a *web.config* file configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="68281-272">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="68281-272">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="68281-273">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="68281-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="68281-274">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="68281-274">Enhanced diagnostic logs</span></span>

<span data-ttu-id="68281-275">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="68281-275">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="68281-276">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素  。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="68281-276">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="68281-277">创建日志文件时，路径中的任何文件夹（上述示例中为 logs  ）由模块创建。</span><span class="sxs-lookup"><span data-stu-id="68281-277">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="68281-278">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="68281-278">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="68281-279">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="68281-279">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="68281-280">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="68281-280">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="68281-281">错误</span><span class="sxs-lookup"><span data-stu-id="68281-281">ERROR</span></span>
* <span data-ttu-id="68281-282">警告</span><span class="sxs-lookup"><span data-stu-id="68281-282">WARNING</span></span>
* <span data-ttu-id="68281-283">信息</span><span class="sxs-lookup"><span data-stu-id="68281-283">INFO</span></span>
* <span data-ttu-id="68281-284">TRACE</span><span class="sxs-lookup"><span data-stu-id="68281-284">TRACE</span></span>

<span data-ttu-id="68281-285">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="68281-285">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="68281-286">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="68281-286">CONSOLE</span></span>
* <span data-ttu-id="68281-287">事件日志</span><span class="sxs-lookup"><span data-stu-id="68281-287">EVENTLOG</span></span>
* <span data-ttu-id="68281-288">文件</span><span class="sxs-lookup"><span data-stu-id="68281-288">FILE</span></span>

<span data-ttu-id="68281-289">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="68281-289">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="68281-290">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; 调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="68281-290">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="68281-291">（默认值：aspnetcore debug.log） </span><span class="sxs-lookup"><span data-stu-id="68281-291">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="68281-292">`ASPNETCORE_MODULE_DEBUG` &ndash; 调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="68281-292">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="68281-293">部署中启用的调试日志记录的时间不要超出排除故障所需的时间  。</span><span class="sxs-lookup"><span data-stu-id="68281-293">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="68281-294">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="68281-294">The size of the log isn't limited.</span></span> <span data-ttu-id="68281-295">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="68281-295">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="68281-296">有关 web.config  文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="68281-296">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="68281-297">修改堆栈大小</span><span class="sxs-lookup"><span data-stu-id="68281-297">Modify the stack size</span></span>

<span data-ttu-id="68281-298">*仅适用于使用进程内托管模型的情况。*</span><span class="sxs-lookup"><span data-stu-id="68281-298">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="68281-299">使用 web.config 中的 `stackSize` 设置（以字节为单位）配置托管堆栈大小  。默认大小为 `1048576` 个字节 (1 MB)。</span><span class="sxs-lookup"><span data-stu-id="68281-299">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="68281-300">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="68281-300">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="68281-301">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="68281-301">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="68281-302">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="68281-302">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="68281-303">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="68281-303">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="68281-304">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="68281-304">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="68281-305">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="68281-305">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="68281-306">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="68281-306">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="68281-307">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="68281-307">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="68281-308">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="68281-308">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="68281-309">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="68281-309">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="68281-310">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="68281-310">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="68281-311">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="68281-311">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="68281-312">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行  。</span><span class="sxs-lookup"><span data-stu-id="68281-312">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="68281-313">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误  。</span><span class="sxs-lookup"><span data-stu-id="68281-313">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="68281-314">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="68281-314">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="68281-315">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="68281-315">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="68281-316">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="68281-316">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="68281-317">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="68281-317">Run the installer.</span></span>
1. <span data-ttu-id="68281-318">将已更新的 applicationHost.config  文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="68281-318">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="68281-319">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="68281-319">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="68281-320">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="68281-320">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="68281-321">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="68281-321">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="68281-322">在托管系统上，导航到 %windir%\System32\inetsrv  。</span><span class="sxs-lookup"><span data-stu-id="68281-322">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="68281-323">找到 aspnetcore.dll  文件。</span><span class="sxs-lookup"><span data-stu-id="68281-323">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="68281-324">右键单击该文件，然后从上下文菜单中选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="68281-324">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="68281-325">选择“详细信息”选项卡  。“文件版本”  和“产品版本”  表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="68281-325">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="68281-326">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp  中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log  。</span><span class="sxs-lookup"><span data-stu-id="68281-326">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="68281-327">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="68281-327">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="68281-328">模块</span><span class="sxs-lookup"><span data-stu-id="68281-328">Module</span></span>

<span data-ttu-id="68281-329">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="68281-329">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="68281-330">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-330">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="68281-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="68281-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="68281-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="68281-333">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="68281-333">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="68281-334">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="68281-334">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="68281-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="68281-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="68281-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="68281-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="68281-338">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="68281-338">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="68281-339">架构</span><span class="sxs-lookup"><span data-stu-id="68281-339">Schema</span></span>

<span data-ttu-id="68281-340">**IIS**</span><span class="sxs-lookup"><span data-stu-id="68281-340">**IIS**</span></span>

* <span data-ttu-id="68281-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="68281-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="68281-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="68281-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="68281-343">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="68281-343">**IIS Express**</span></span>

* <span data-ttu-id="68281-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="68281-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="68281-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="68281-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="68281-346">配置</span><span class="sxs-lookup"><span data-stu-id="68281-346">Configuration</span></span>

<span data-ttu-id="68281-347">**IIS**</span><span class="sxs-lookup"><span data-stu-id="68281-347">**IIS**</span></span>

* <span data-ttu-id="68281-348">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="68281-348">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="68281-349">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="68281-349">**IIS Express**</span></span>

* <span data-ttu-id="68281-350">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="68281-350">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="68281-351"> iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="68281-351">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="68281-352">可以通过在 applicationHost.config  文件中搜索 aspnetcore  找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="68281-352">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="68281-353">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="68281-353">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="68281-354">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="68281-354">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="68281-355">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="68281-355">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="68281-356">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="68281-356">Supported Windows versions:</span></span>

* <span data-ttu-id="68281-357">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="68281-357">Windows 7 or later</span></span>
* <span data-ttu-id="68281-358">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="68281-358">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="68281-359">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="68281-359">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="68281-360">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="68281-360">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="68281-361">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="68281-361">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="68281-362">托管模型</span><span class="sxs-lookup"><span data-stu-id="68281-362">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="68281-363">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="68281-363">In-process hosting model</span></span>

<span data-ttu-id="68281-364">若要配置用于进程内托管的应用，请将 `<AspNetCoreHostingModel>` 属性添加到值为 `InProcess`（进程外托管使用 `OutOfProcess` 进行设置）的应用项目文件：</span><span class="sxs-lookup"><span data-stu-id="68281-364">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="68281-365">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="68281-365">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="68281-366">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="68281-366">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="68281-367">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="68281-367">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="68281-368">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="68281-368">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="68281-369">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="68281-369">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="68281-370">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="68281-370">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="68281-371">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="68281-371">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="68281-372">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="68281-372">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="68281-373">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="68281-373">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="68281-374">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="68281-374">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="68281-375">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="68281-375">Use one app pool per app.</span></span>

* <span data-ttu-id="68281-376">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="68281-376">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="68281-377">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="68281-377">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="68281-378">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="68281-378">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="68281-379">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="68281-379">Client disconnects are detected.</span></span> <span data-ttu-id="68281-380">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="68281-380">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="68281-381">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv   ）。</span><span class="sxs-lookup"><span data-stu-id="68281-381">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="68281-382">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="68281-382">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="68281-383">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="68281-383">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="68281-384">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="68281-384">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="68281-385">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="68281-385">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="68281-386">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="68281-386">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="68281-387">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="68281-387">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="68281-388">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="68281-388">Out-of-process hosting model</span></span>

<span data-ttu-id="68281-389">若要配置进程外托管应用，请在项目文件中使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="68281-389">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="68281-390">不指定 `<AspNetCoreHostingModel>` 属性。</span><span class="sxs-lookup"><span data-stu-id="68281-390">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="68281-391">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="68281-391">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="68281-392">将 `<AspNetCoreHostingModel>` 属性的值设为 `OutOfProcess`（进程内托管设为 `InProcess`）：</span><span class="sxs-lookup"><span data-stu-id="68281-392">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="68281-393">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="68281-393">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="68281-394">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="68281-394">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="68281-395">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="68281-395">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="68281-396">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="68281-396">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="68281-397">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="68281-397">Hosting model changes</span></span>

<span data-ttu-id="68281-398">如果 `hostingModel` 设置在 web.config  文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="68281-398">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="68281-399">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="68281-399">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="68281-400">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="68281-400">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="68281-401">进程名</span><span class="sxs-lookup"><span data-stu-id="68281-401">Process name</span></span>

<span data-ttu-id="68281-402">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="68281-402">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="68281-403">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="68281-403">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="68281-404">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="68281-404">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="68281-405">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="68281-405">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="68281-406">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-406">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="68281-407">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="68281-407">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="68281-408">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="68281-408">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="68281-409">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="68281-409">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="68281-410">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="68281-410">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="68281-411">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="68281-411">Configuration with web.config</span></span>

<span data-ttu-id="68281-412">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="68281-412">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="68281-413">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="68281-413">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="68281-414">以下 web.config  发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="68281-414">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="68281-415">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="68281-415">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="68281-416">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="68281-416">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="68281-417">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="68281-417">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="68281-418">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="68281-418">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="68281-419">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="68281-419">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="68281-420">特性</span><span class="sxs-lookup"><span data-stu-id="68281-420">Attribute</span></span> | <span data-ttu-id="68281-421">说明</span><span class="sxs-lookup"><span data-stu-id="68281-421">Description</span></span> | <span data-ttu-id="68281-422">默认</span><span class="sxs-lookup"><span data-stu-id="68281-422">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="68281-423">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-423">Optional string attribute.</span></span></p><p><span data-ttu-id="68281-424">processPath  中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="68281-424">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="68281-425">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="68281-425">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="68281-426">如果为 true，将禁止显示“502.5 - 进程失败”  页面，而会优先显示 web.config  中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="68281-426">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="68281-427">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="68281-427">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="68281-428">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="68281-428">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="68281-429">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="68281-429">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="68281-430">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-430">Optional string attribute.</span></span></p><p><span data-ttu-id="68281-431">将托管模型指定为进程内 (`InProcess`) 或进程外 (`OutOfProcess`)。</span><span class="sxs-lookup"><span data-stu-id="68281-431">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="68281-432">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-432">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-433">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="68281-433">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="68281-434">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="68281-434">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="68281-435">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="68281-435">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="68281-436">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="68281-436">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="68281-437">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="68281-437">Default: `1`</span></span><br><span data-ttu-id="68281-438">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="68281-438">Min: `1`</span></span><br><span data-ttu-id="68281-439">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="68281-439">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="68281-440">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-440">Required string attribute.</span></span></p><p><span data-ttu-id="68281-441">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="68281-441">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="68281-442">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="68281-442">Relative paths are supported.</span></span> <span data-ttu-id="68281-443">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="68281-443">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="68281-444">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-444">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-445">指定允许 processPath  中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="68281-445">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="68281-446">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="68281-446">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="68281-447">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="68281-447">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="68281-448">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="68281-448">Default: `10`</span></span><br><span data-ttu-id="68281-449">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="68281-449">Min: `0`</span></span><br><span data-ttu-id="68281-450">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="68281-450">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="68281-451">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="68281-451">Optional timespan attribute.</span></span></p><p><span data-ttu-id="68281-452">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="68281-452">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="68281-453">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="68281-453">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="68281-454">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="68281-454">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="68281-455">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="68281-455">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="68281-456">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="68281-456">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="68281-457">在分钟或秒钟值中使用“60”  会导致“500 - 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="68281-457">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="68281-458">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="68281-458">Default: `00:02:00`</span></span><br><span data-ttu-id="68281-459">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="68281-459">Min: `00:00:00`</span></span><br><span data-ttu-id="68281-460">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="68281-460">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="68281-461">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-461">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-462">检测到 app_offline.htm  文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="68281-462">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="68281-463">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="68281-463">Default: `10`</span></span><br><span data-ttu-id="68281-464">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="68281-464">Min: `0`</span></span><br><span data-ttu-id="68281-465">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="68281-465">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="68281-466">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-466">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-467">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="68281-467">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="68281-468">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="68281-468">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="68281-469">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute  次。</span><span class="sxs-lookup"><span data-stu-id="68281-469">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="68281-470">值 0（零）  不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="68281-470">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="68281-471">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="68281-471">Default: `120`</span></span><br><span data-ttu-id="68281-472">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="68281-472">Min: `0`</span></span><br><span data-ttu-id="68281-473">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="68281-473">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="68281-474">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="68281-474">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="68281-475">如果为 true，processPath  中指定的 进程的 stdout  和 stderr  将重定向到 stdoutLogFile  中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="68281-475">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="68281-476">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-476">Optional string attribute.</span></span></p><p><span data-ttu-id="68281-477">指定在其中记录 processPath  中指定进程的 stdout  和 stderr  的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="68281-477">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="68281-478">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="68281-478">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="68281-479">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="68281-479">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="68281-480">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="68281-480">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="68281-481">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log  ) 添加到 stdoutLogFile  路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="68281-481">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="68281-482">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs  文件夹中保存为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="68281-482">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="68281-483">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="68281-483">Setting environment variables</span></span>

<span data-ttu-id="68281-484">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-484">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="68281-485">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-485">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="68281-486">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-486">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="68281-487">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-487">The following example sets two environment variables.</span></span> <span data-ttu-id="68281-488">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="68281-488">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="68281-489">开发人员可能会暂时在 web.config  文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="68281-489">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="68281-490">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="68281-490">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!NOTE]
> <span data-ttu-id="68281-491">直接在 web.config  中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml  ）或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="68281-491">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="68281-492">此方法在发布项目时设置 web.config  中的环境：</span><span class="sxs-lookup"><span data-stu-id="68281-492">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="68281-493">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="68281-493">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="68281-494">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="68281-494">app_offline.htm</span></span>

<span data-ttu-id="68281-495">如果在应用的根目录中检测到名为 “app_offline.htm”  的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="68281-495">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="68281-496">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="68281-496">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="68281-497">存在 app_offline.htm  文件时，ASP.NET Core 模块会通过发送回 app_offline.htm  文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="68281-497">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="68281-498">删除 app_offline.htm  文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="68281-498">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="68281-499">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="68281-499">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="68281-500">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="68281-500">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="68281-501">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="68281-501">Start-up error page</span></span>

<span data-ttu-id="68281-502">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="68281-502">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="68281-503">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="68281-503">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="68281-504">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="68281-504">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="68281-505">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”  状态代码页。</span><span class="sxs-lookup"><span data-stu-id="68281-505">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="68281-506">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="68281-506">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="68281-507">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="68281-507">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="68281-508">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="68281-508">Log creation and redirection</span></span>

<span data-ttu-id="68281-509">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="68281-509">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="68281-510">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="68281-510">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="68281-511">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="68281-511">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="68281-512">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="68281-512">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="68281-513">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="68281-513">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="68281-514">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="68281-514">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="68281-515">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="68281-515">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="68281-516">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="68281-516">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="68281-517">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="68281-517">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="68281-518">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="68281-518">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="68281-519">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log  ) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout  ）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="68281-519">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="68281-520">如果 `stdoutLogFile` 路径以 stdout  结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="68281-520">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="68281-521">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="68281-521">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="68281-522">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="68281-522">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="68281-523">以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="68281-523">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="68281-524">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="68281-524">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="68281-525">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="68281-525">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="68281-526">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="68281-526">Enhanced diagnostic logs</span></span>

<span data-ttu-id="68281-527">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="68281-527">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="68281-528">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素  。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="68281-528">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="68281-529">提供给 `<handlerSetting>` 值（上述示例中为 logs  ）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="68281-529">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="68281-530">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="68281-530">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="68281-531">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="68281-531">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="68281-532">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="68281-532">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="68281-533">错误</span><span class="sxs-lookup"><span data-stu-id="68281-533">ERROR</span></span>
* <span data-ttu-id="68281-534">警告</span><span class="sxs-lookup"><span data-stu-id="68281-534">WARNING</span></span>
* <span data-ttu-id="68281-535">信息</span><span class="sxs-lookup"><span data-stu-id="68281-535">INFO</span></span>
* <span data-ttu-id="68281-536">TRACE</span><span class="sxs-lookup"><span data-stu-id="68281-536">TRACE</span></span>

<span data-ttu-id="68281-537">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="68281-537">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="68281-538">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="68281-538">CONSOLE</span></span>
* <span data-ttu-id="68281-539">事件日志</span><span class="sxs-lookup"><span data-stu-id="68281-539">EVENTLOG</span></span>
* <span data-ttu-id="68281-540">文件</span><span class="sxs-lookup"><span data-stu-id="68281-540">FILE</span></span>

<span data-ttu-id="68281-541">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="68281-541">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="68281-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; 调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="68281-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="68281-543">（默认值：aspnetcore debug.log） </span><span class="sxs-lookup"><span data-stu-id="68281-543">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="68281-544">`ASPNETCORE_MODULE_DEBUG` &ndash; 调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="68281-544">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="68281-545">部署中启用的调试日志记录的时间不要超出排除故障所需的时间  。</span><span class="sxs-lookup"><span data-stu-id="68281-545">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="68281-546">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="68281-546">The size of the log isn't limited.</span></span> <span data-ttu-id="68281-547">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="68281-547">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="68281-548">有关 web.config  文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="68281-548">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="68281-549">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="68281-549">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="68281-550">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="68281-550">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="68281-551">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="68281-551">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="68281-552">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="68281-552">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="68281-553">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="68281-553">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="68281-554">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="68281-554">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="68281-555">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="68281-555">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="68281-556">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="68281-556">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="68281-557">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="68281-557">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="68281-558">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="68281-558">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="68281-559">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="68281-559">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="68281-560">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="68281-560">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="68281-561">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行  。</span><span class="sxs-lookup"><span data-stu-id="68281-561">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="68281-562">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误  。</span><span class="sxs-lookup"><span data-stu-id="68281-562">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="68281-563">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="68281-563">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="68281-564">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="68281-564">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="68281-565">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="68281-565">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="68281-566">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="68281-566">Run the installer.</span></span>
1. <span data-ttu-id="68281-567">将已更新的 applicationHost.config  文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="68281-567">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="68281-568">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="68281-568">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="68281-569">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="68281-569">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="68281-570">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="68281-570">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="68281-571">在托管系统上，导航到 %windir%\System32\inetsrv  。</span><span class="sxs-lookup"><span data-stu-id="68281-571">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="68281-572">找到 aspnetcore.dll  文件。</span><span class="sxs-lookup"><span data-stu-id="68281-572">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="68281-573">右键单击该文件，然后从上下文菜单中选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="68281-573">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="68281-574">选择“详细信息”选项卡  。“文件版本”  和“产品版本”  表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="68281-574">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="68281-575">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp  中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log  。</span><span class="sxs-lookup"><span data-stu-id="68281-575">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="68281-576">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="68281-576">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="68281-577">模块</span><span class="sxs-lookup"><span data-stu-id="68281-577">Module</span></span>

<span data-ttu-id="68281-578">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="68281-578">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="68281-579">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-579">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="68281-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="68281-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="68281-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="68281-582">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="68281-582">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="68281-583">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="68281-583">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="68281-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="68281-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="68281-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="68281-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="68281-587">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="68281-587">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="68281-588">架构</span><span class="sxs-lookup"><span data-stu-id="68281-588">Schema</span></span>

<span data-ttu-id="68281-589">**IIS**</span><span class="sxs-lookup"><span data-stu-id="68281-589">**IIS**</span></span>

* <span data-ttu-id="68281-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="68281-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="68281-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="68281-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="68281-592">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="68281-592">**IIS Express**</span></span>

* <span data-ttu-id="68281-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="68281-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="68281-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="68281-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="68281-595">配置</span><span class="sxs-lookup"><span data-stu-id="68281-595">Configuration</span></span>

<span data-ttu-id="68281-596">**IIS**</span><span class="sxs-lookup"><span data-stu-id="68281-596">**IIS**</span></span>

* <span data-ttu-id="68281-597">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="68281-597">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="68281-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="68281-598">**IIS Express**</span></span>

* <span data-ttu-id="68281-599">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="68281-599">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="68281-600"> iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="68281-600">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="68281-601">可以通过在 applicationHost.config  文件中搜索 aspnetcore  找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="68281-601">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="68281-602">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于将 Web 请求转发到后端 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="68281-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="68281-603">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="68281-603">Supported Windows versions:</span></span>

* <span data-ttu-id="68281-604">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="68281-604">Windows 7 or later</span></span>
* <span data-ttu-id="68281-605">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="68281-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="68281-606">该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="68281-606">The module only works with Kestrel.</span></span> <span data-ttu-id="68281-607">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="68281-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="68281-608">由于 ASP.NET Core 应用在独立于 IIS 辅助进程的进程中运行，因此该模块还处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="68281-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="68281-609">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="68281-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="68281-610">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的 IIS 中在进程内运行的 ASP.NET 4.x 应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="68281-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="68281-611">下图说明了 IIS、ASP.NET Core 模块和应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="68281-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模块](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="68281-613">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="68281-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="68281-614">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="68281-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="68281-615">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="68281-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="68281-616">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="68281-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="68281-617">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="68281-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="68281-618">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="68281-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="68281-619">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="68281-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="68281-620">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="68281-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="68281-621">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="68281-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="68281-622">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="68281-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="68281-623">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="68281-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="68281-624">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="68281-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="68281-625">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="68281-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="68281-626">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="68281-627">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="68281-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="68281-628">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="68281-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="68281-629">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="68281-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="68281-630">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="68281-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="68281-631">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="68281-631">Configuration with web.config</span></span>

<span data-ttu-id="68281-632">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="68281-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="68281-633">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="68281-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="68281-634">以下 web.config  发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="68281-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="68281-635">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="68281-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="68281-636">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="68281-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="68281-637">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="68281-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="68281-638">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="68281-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="68281-639">特性</span><span class="sxs-lookup"><span data-stu-id="68281-639">Attribute</span></span> | <span data-ttu-id="68281-640">说明</span><span class="sxs-lookup"><span data-stu-id="68281-640">Description</span></span> | <span data-ttu-id="68281-641">默认</span><span class="sxs-lookup"><span data-stu-id="68281-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="68281-642">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-642">Optional string attribute.</span></span></p><p><span data-ttu-id="68281-643">processPath  中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="68281-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="68281-644">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="68281-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="68281-645">如果为 true，将禁止显示“502.5 - 进程失败”  页面，而会优先显示 web.config  中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="68281-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="68281-646">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="68281-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="68281-647">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="68281-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="68281-648">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="68281-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="68281-649">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-650">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="68281-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="68281-651">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="68281-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="68281-652">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="68281-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="68281-653">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="68281-653">Default: `1`</span></span><br><span data-ttu-id="68281-654">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="68281-654">Min: `1`</span></span><br><span data-ttu-id="68281-655">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="68281-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="68281-656">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-656">Required string attribute.</span></span></p><p><span data-ttu-id="68281-657">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="68281-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="68281-658">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="68281-658">Relative paths are supported.</span></span> <span data-ttu-id="68281-659">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="68281-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="68281-660">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-661">指定允许 processPath  中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="68281-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="68281-662">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="68281-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="68281-663">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="68281-663">Default: `10`</span></span><br><span data-ttu-id="68281-664">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="68281-664">Min: `0`</span></span><br><span data-ttu-id="68281-665">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="68281-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="68281-666">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="68281-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="68281-667">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="68281-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="68281-668">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="68281-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="68281-669">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="68281-669">Default: `00:02:00`</span></span><br><span data-ttu-id="68281-670">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="68281-670">Min: `00:00:00`</span></span><br><span data-ttu-id="68281-671">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="68281-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="68281-672">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-673">检测到 app_offline.htm  文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="68281-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="68281-674">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="68281-674">Default: `10`</span></span><br><span data-ttu-id="68281-675">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="68281-675">Min: `0`</span></span><br><span data-ttu-id="68281-676">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="68281-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="68281-677">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="68281-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="68281-678">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="68281-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="68281-679">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="68281-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="68281-680">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute  次。</span><span class="sxs-lookup"><span data-stu-id="68281-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="68281-681">值 0（零）  不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="68281-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="68281-682">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="68281-682">Default: `120`</span></span><br><span data-ttu-id="68281-683">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="68281-683">Min: `0`</span></span><br><span data-ttu-id="68281-684">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="68281-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="68281-685">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="68281-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="68281-686">如果为 true，processPath  中指定的 进程的 stdout  和 stderr  将重定向到 stdoutLogFile  中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="68281-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="68281-687">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="68281-687">Optional string attribute.</span></span></p><p><span data-ttu-id="68281-688">指定在其中记录 processPath  中指定进程的 stdout  和 stderr  的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="68281-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="68281-689">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="68281-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="68281-690">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="68281-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="68281-691">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="68281-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="68281-692">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log  ) 添加到 stdoutLogFile  路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="68281-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="68281-693">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs  文件夹中保存为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="68281-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="68281-694">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="68281-694">Setting environment variables</span></span>

<span data-ttu-id="68281-695">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="68281-696">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="68281-697">本部分中设置的环境变量与使用同一名称设置的系统环境变量冲突。</span><span class="sxs-lookup"><span data-stu-id="68281-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="68281-698">如果在 web.config  文件以及 Windows 中的系统级别中同时设置了环境变量，则 web.config  文件中的值将被追加到系统环境变量值（例如，`ASPNETCORE_ENVIRONMENT: Development;Development`），这将阻止应用启动。</span><span class="sxs-lookup"><span data-stu-id="68281-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="68281-699">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="68281-699">The following example sets two environment variables.</span></span> <span data-ttu-id="68281-700">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="68281-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="68281-701">开发人员可能会暂时在 web.config  文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="68281-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="68281-702">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="68281-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="68281-703">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="68281-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="68281-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="68281-704">app_offline.htm</span></span>

<span data-ttu-id="68281-705">如果在应用的根目录中检测到名为 “app_offline.htm”  的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="68281-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="68281-706">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="68281-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="68281-707">存在 app_offline.htm  文件时，ASP.NET Core 模块会通过发送回 app_offline.htm  文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="68281-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="68281-708">删除 app_offline.htm  文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="68281-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="68281-709">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="68281-709">Start-up error page</span></span>

<span data-ttu-id="68281-710">如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”  状态代码页。</span><span class="sxs-lookup"><span data-stu-id="68281-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="68281-711">若要禁止显示此页面并还原为默认 IIS 502 状态代码页面，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="68281-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="68281-712">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="68281-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![“502.5 进程失败”状态代码页面](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="68281-714">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="68281-714">Log creation and redirection</span></span>

<span data-ttu-id="68281-715">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="68281-715">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="68281-716">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="68281-716">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="68281-717">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="68281-717">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="68281-718">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="68281-718">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="68281-719">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="68281-719">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="68281-720">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="68281-720">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="68281-721">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="68281-721">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="68281-722">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="68281-722">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="68281-723">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="68281-723">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="68281-724">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="68281-724">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="68281-725">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log  ) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout  ）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="68281-725">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="68281-726">如果 `stdoutLogFile` 路径以 stdout  结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="68281-726">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="68281-727">以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="68281-727">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="68281-728">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="68281-728">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="68281-729">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="68281-729">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

<span data-ttu-id="68281-730">提供给 `<handlerSetting>` 值（上述示例中为 logs  ）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="68281-730">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="68281-731">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="68281-731">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="68281-732">有关 web.config  文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="68281-732">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="68281-733">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="68281-733">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="68281-734">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="68281-734">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="68281-735">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="68281-735">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="68281-736">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="68281-736">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="68281-737">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="68281-737">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="68281-738">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="68281-738">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="68281-739">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="68281-739">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="68281-740">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="68281-740">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="68281-741">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="68281-741">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="68281-742">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="68281-742">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="68281-743">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="68281-743">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="68281-744">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行  。</span><span class="sxs-lookup"><span data-stu-id="68281-744">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="68281-745">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误  。</span><span class="sxs-lookup"><span data-stu-id="68281-745">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="68281-746">使用 IIS 共享配置时，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="68281-746">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="68281-747">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="68281-747">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="68281-748">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="68281-748">Run the installer.</span></span>
1. <span data-ttu-id="68281-749">将已更新的 applicationHost.config  文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="68281-749">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="68281-750">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="68281-750">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="68281-751">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="68281-751">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="68281-752">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="68281-752">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="68281-753">在托管系统上，导航到 %windir%\System32\inetsrv  。</span><span class="sxs-lookup"><span data-stu-id="68281-753">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="68281-754">找到 aspnetcore.dll  文件。</span><span class="sxs-lookup"><span data-stu-id="68281-754">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="68281-755">右键单击该文件，然后从上下文菜单中选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="68281-755">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="68281-756">选择“详细信息”选项卡  。“文件版本”  和“产品版本”  表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="68281-756">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="68281-757">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp  中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log  。</span><span class="sxs-lookup"><span data-stu-id="68281-757">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="68281-758">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="68281-758">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="68281-759">模块</span><span class="sxs-lookup"><span data-stu-id="68281-759">Module</span></span>

<span data-ttu-id="68281-760">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="68281-760">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="68281-761">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-761">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="68281-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="68281-763">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="68281-763">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="68281-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="68281-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="68281-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="68281-766">架构</span><span class="sxs-lookup"><span data-stu-id="68281-766">Schema</span></span>

<span data-ttu-id="68281-767">**IIS**</span><span class="sxs-lookup"><span data-stu-id="68281-767">**IIS**</span></span>

* <span data-ttu-id="68281-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="68281-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="68281-769">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="68281-769">**IIS Express**</span></span>

* <span data-ttu-id="68281-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="68281-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="68281-771">配置</span><span class="sxs-lookup"><span data-stu-id="68281-771">Configuration</span></span>

<span data-ttu-id="68281-772">**IIS**</span><span class="sxs-lookup"><span data-stu-id="68281-772">**IIS**</span></span>

* <span data-ttu-id="68281-773">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="68281-773">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="68281-774">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="68281-774">**IIS Express**</span></span>

* <span data-ttu-id="68281-775">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="68281-775">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="68281-776"> iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="68281-776">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="68281-777">可以通过在 applicationHost.config  文件中搜索 aspnetcore  找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="68281-777">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="68281-778">其他资源</span><span class="sxs-lookup"><span data-stu-id="68281-778">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="68281-779">ASP.NET Core 模块 GitHub 存储库（引用源）</span><span class="sxs-lookup"><span data-stu-id="68281-779">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
