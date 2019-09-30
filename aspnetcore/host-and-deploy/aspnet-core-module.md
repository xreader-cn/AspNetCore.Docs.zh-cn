---
title: ASP.NET Core 模块
author: guardrex
description: 了解如何配置 ASP.NET Core 模块以托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/24/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 811aafce6686b446440b146efd7449b598ed1722
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248350"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="3f7de-103">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="3f7de-103">ASP.NET Core Module</span></span>

<span data-ttu-id="3f7de-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3f7de-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f7de-105">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="3f7de-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="3f7de-106">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="3f7de-107">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="3f7de-108">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="3f7de-108">Supported Windows versions:</span></span>

* <span data-ttu-id="3f7de-109">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3f7de-109">Windows 7 or later</span></span>
* <span data-ttu-id="3f7de-110">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3f7de-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="3f7de-111">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="3f7de-112">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f7de-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="3f7de-113">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="3f7de-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="3f7de-114">托管模型</span><span class="sxs-lookup"><span data-stu-id="3f7de-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="3f7de-115">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="3f7de-115">In-process hosting model</span></span>

<span data-ttu-id="3f7de-116">ASP.NET Core 应用默认为进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="3f7de-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="3f7de-117">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="3f7de-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="3f7de-118">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="3f7de-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="3f7de-119">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3f7de-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="3f7de-120">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="3f7de-121">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="3f7de-122">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="3f7de-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="3f7de-123">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="3f7de-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="3f7de-124">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="3f7de-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="3f7de-125">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="3f7de-125">Use one app pool per app.</span></span>

* <span data-ttu-id="3f7de-126">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="3f7de-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="3f7de-127">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="3f7de-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="3f7de-128">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="3f7de-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="3f7de-129">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="3f7de-129">Client disconnects are detected.</span></span> <span data-ttu-id="3f7de-130">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="3f7de-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="3f7de-131">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="3f7de-132">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="3f7de-133">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="3f7de-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="3f7de-134">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="3f7de-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="3f7de-135">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="3f7de-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="3f7de-136">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="3f7de-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="3f7de-137">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="3f7de-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="3f7de-138">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="3f7de-138">Out-of-process hosting model</span></span>

<span data-ttu-id="3f7de-139">若要配置用于进程外托管的应用，请将 `<AspNetCoreHostingModel>` 属性的值设置为 `OutOfProcess`（进程内托管使用 `InProcess` 进行设置，这是默认值）：</span><span class="sxs-lookup"><span data-stu-id="3f7de-139">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`, which is the default value):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="3f7de-140">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-140">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="3f7de-141">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3f7de-141">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="3f7de-142">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-142">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="3f7de-143">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="3f7de-143">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="3f7de-144">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="3f7de-144">Hosting model changes</span></span>

<span data-ttu-id="3f7de-145">如果 `hostingModel` 设置在 web.config 文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-145">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="3f7de-146">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="3f7de-146">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="3f7de-147">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-147">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="3f7de-148">进程名</span><span class="sxs-lookup"><span data-stu-id="3f7de-148">Process name</span></span>

<span data-ttu-id="3f7de-149">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-149">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="3f7de-150">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="3f7de-150">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="3f7de-151">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="3f7de-151">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="3f7de-152">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="3f7de-152">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="3f7de-153">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-153">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="3f7de-154">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="3f7de-154">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="3f7de-155">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="3f7de-155">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="3f7de-156">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="3f7de-156">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="3f7de-157">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-157">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="3f7de-158">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="3f7de-158">Configuration with web.config</span></span>

<span data-ttu-id="3f7de-159">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="3f7de-159">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="3f7de-160">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="3f7de-160">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="3f7de-161">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="3f7de-161">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="3f7de-162">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="3f7de-162">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="3f7de-163">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-163">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="3f7de-164">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-164">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="3f7de-165">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="3f7de-165">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="3f7de-166">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="3f7de-166">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="3f7de-167">特性</span><span class="sxs-lookup"><span data-stu-id="3f7de-167">Attribute</span></span> | <span data-ttu-id="3f7de-168">说明</span><span class="sxs-lookup"><span data-stu-id="3f7de-168">Description</span></span> | <span data-ttu-id="3f7de-169">默认</span><span class="sxs-lookup"><span data-stu-id="3f7de-169">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="3f7de-170">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-170">Optional string attribute.</span></span></p><p><span data-ttu-id="3f7de-171">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="3f7de-171">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="3f7de-172">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-172">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="3f7de-173">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="3f7de-173">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="3f7de-174">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-174">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="3f7de-175">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-175">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="3f7de-176">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="3f7de-176">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="3f7de-177">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-177">Optional string attribute.</span></span></p><p><span data-ttu-id="3f7de-178">将托管模型指定为进程内 (`InProcess`) 或进程外 (`OutOfProcess`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-178">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `InProcess` |
| `processesPerApplication` | <p><span data-ttu-id="3f7de-179">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-179">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-180">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="3f7de-180">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="3f7de-181">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-181">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="3f7de-182">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-182">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="3f7de-183">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-183">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="3f7de-184">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="3f7de-184">Default: `1`</span></span><br><span data-ttu-id="3f7de-185">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="3f7de-185">Min: `1`</span></span><br><span data-ttu-id="3f7de-186">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="3f7de-186">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="3f7de-187">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-187">Required string attribute.</span></span></p><p><span data-ttu-id="3f7de-188">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-188">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="3f7de-189">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-189">Relative paths are supported.</span></span> <span data-ttu-id="3f7de-190">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="3f7de-190">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="3f7de-191">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-191">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-192">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="3f7de-192">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="3f7de-193">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-193">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="3f7de-194">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="3f7de-194">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="3f7de-195">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="3f7de-195">Default: `10`</span></span><br><span data-ttu-id="3f7de-196">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="3f7de-196">Min: `0`</span></span><br><span data-ttu-id="3f7de-197">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="3f7de-197">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="3f7de-198">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-198">Optional timespan attribute.</span></span></p><p><span data-ttu-id="3f7de-199">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="3f7de-199">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="3f7de-200">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-200">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="3f7de-201">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="3f7de-201">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="3f7de-202">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-202">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="3f7de-203">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="3f7de-203">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="3f7de-204">在分钟或秒钟值中使用“60”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="3f7de-204">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="3f7de-205">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="3f7de-205">Default: `00:02:00`</span></span><br><span data-ttu-id="3f7de-206">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="3f7de-206">Min: `00:00:00`</span></span><br><span data-ttu-id="3f7de-207">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="3f7de-207">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="3f7de-208">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-208">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-209">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-209">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="3f7de-210">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="3f7de-210">Default: `10`</span></span><br><span data-ttu-id="3f7de-211">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="3f7de-211">Min: `0`</span></span><br><span data-ttu-id="3f7de-212">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="3f7de-212">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="3f7de-213">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-213">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-214">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-214">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="3f7de-215">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-215">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="3f7de-216">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="3f7de-216">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="3f7de-217">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="3f7de-217">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="3f7de-218">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="3f7de-218">Default: `120`</span></span><br><span data-ttu-id="3f7de-219">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="3f7de-219">Min: `0`</span></span><br><span data-ttu-id="3f7de-220">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="3f7de-220">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="3f7de-221">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-221">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="3f7de-222">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="3f7de-222">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="3f7de-223">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-223">Optional string attribute.</span></span></p><p><span data-ttu-id="3f7de-224">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-224">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="3f7de-225">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="3f7de-225">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="3f7de-226">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-226">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="3f7de-227">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="3f7de-227">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="3f7de-228">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="3f7de-228">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="3f7de-229">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="3f7de-229">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="3f7de-230">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="3f7de-230">Setting environment variables</span></span>

<span data-ttu-id="3f7de-231">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-231">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="3f7de-232">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-232">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="3f7de-233">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-233">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="3f7de-234">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-234">The following example sets two environment variables.</span></span> <span data-ttu-id="3f7de-235">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-235">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="3f7de-236">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-236">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="3f7de-237">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-237">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="3f7de-238">直接在 web.config 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml）或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="3f7de-238">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="3f7de-239">此方法在发布项目时设置 web.config 中的环境：</span><span class="sxs-lookup"><span data-stu-id="3f7de-239">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="3f7de-240">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-240">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="3f7de-241">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="3f7de-241">app_offline.htm</span></span>

<span data-ttu-id="3f7de-242">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-242">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="3f7de-243">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-243">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="3f7de-244">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-244">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="3f7de-245">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="3f7de-245">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="3f7de-246">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="3f7de-246">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="3f7de-247">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="3f7de-247">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="3f7de-248">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="3f7de-248">Start-up error page</span></span>

<span data-ttu-id="3f7de-249">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="3f7de-249">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="3f7de-250">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="3f7de-250">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="3f7de-251">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="3f7de-251">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="3f7de-252">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="3f7de-252">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="3f7de-253">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-253">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="3f7de-254">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-254">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="3f7de-255">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="3f7de-255">Log creation and redirection</span></span>

<span data-ttu-id="3f7de-256">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="3f7de-256">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="3f7de-257">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="3f7de-257">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="3f7de-258">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-258">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="3f7de-259">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-259">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="3f7de-260">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="3f7de-260">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="3f7de-261">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="3f7de-261">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="3f7de-262">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="3f7de-262">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="3f7de-263">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="3f7de-263">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f7de-264">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-264">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="3f7de-265">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="3f7de-265">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="3f7de-266">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="3f7de-266">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="3f7de-267">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="3f7de-267">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="3f7de-268">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="3f7de-268">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="3f7de-269">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="3f7de-269">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="3f7de-270">以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="3f7de-270">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="3f7de-271">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-271">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="3f7de-272">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="3f7de-272">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="3f7de-273">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="3f7de-273">Enhanced diagnostic logs</span></span>

<span data-ttu-id="3f7de-274">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="3f7de-274">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="3f7de-275">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="3f7de-275">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="3f7de-276">创建日志文件时，路径中的任何文件夹（上述示例中为 logs）由模块创建。</span><span class="sxs-lookup"><span data-stu-id="3f7de-276">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="3f7de-277">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-277">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="3f7de-278">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-278">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="3f7de-279">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="3f7de-279">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="3f7de-280">错误</span><span class="sxs-lookup"><span data-stu-id="3f7de-280">ERROR</span></span>
* <span data-ttu-id="3f7de-281">警告</span><span class="sxs-lookup"><span data-stu-id="3f7de-281">WARNING</span></span>
* <span data-ttu-id="3f7de-282">信息</span><span class="sxs-lookup"><span data-stu-id="3f7de-282">INFO</span></span>
* <span data-ttu-id="3f7de-283">TRACE</span><span class="sxs-lookup"><span data-stu-id="3f7de-283">TRACE</span></span>

<span data-ttu-id="3f7de-284">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="3f7de-284">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="3f7de-285">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="3f7de-285">CONSOLE</span></span>
* <span data-ttu-id="3f7de-286">事件日志</span><span class="sxs-lookup"><span data-stu-id="3f7de-286">EVENTLOG</span></span>
* <span data-ttu-id="3f7de-287">文件</span><span class="sxs-lookup"><span data-stu-id="3f7de-287">FILE</span></span>

<span data-ttu-id="3f7de-288">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="3f7de-288">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="3f7de-289">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; 调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-289">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="3f7de-290">（默认值：aspnetcore debug.log）</span><span class="sxs-lookup"><span data-stu-id="3f7de-290">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="3f7de-291">`ASPNETCORE_MODULE_DEBUG` &ndash; 调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-291">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="3f7de-292">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="3f7de-292">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="3f7de-293">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="3f7de-293">The size of the log isn't limited.</span></span> <span data-ttu-id="3f7de-294">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="3f7de-294">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="3f7de-295">有关 web.config 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-295">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="3f7de-296">修改堆栈大小</span><span class="sxs-lookup"><span data-stu-id="3f7de-296">Modify the stack size</span></span>

<span data-ttu-id="3f7de-297">*仅适用于使用进程内托管模型的情况。*</span><span class="sxs-lookup"><span data-stu-id="3f7de-297">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="3f7de-298">使用 `stackSize` 设置（以字节为单位）配置托管堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="3f7de-298">Configure the managed stack size using the `stackSize` setting in bytes.</span></span> <span data-ttu-id="3f7de-299">默认大小为 `1048576` 个字节 (1 MB)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-299">The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="3f7de-300">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="3f7de-300">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="3f7de-301">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="3f7de-301">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="3f7de-302">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="3f7de-302">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="3f7de-303">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="3f7de-303">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="3f7de-304">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="3f7de-304">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="3f7de-305">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-305">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="3f7de-306">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-306">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="3f7de-307">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="3f7de-307">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="3f7de-308">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-308">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="3f7de-309">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-309">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="3f7de-310">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-310">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="3f7de-311">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="3f7de-311">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="3f7de-312">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="3f7de-312">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="3f7de-313">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="3f7de-313">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="3f7de-314">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="3f7de-314">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="3f7de-315">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="3f7de-315">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="3f7de-316">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-316">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="3f7de-317">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="3f7de-317">Run the installer.</span></span>
1. <span data-ttu-id="3f7de-318">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="3f7de-318">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="3f7de-319">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-319">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="3f7de-320">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="3f7de-320">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="3f7de-321">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3f7de-321">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="3f7de-322">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="3f7de-322">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="3f7de-323">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="3f7de-323">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="3f7de-324">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="3f7de-324">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="3f7de-325">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="3f7de-325">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="3f7de-326">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="3f7de-326">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="3f7de-327">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="3f7de-327">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="3f7de-328">模块</span><span class="sxs-lookup"><span data-stu-id="3f7de-328">Module</span></span>

<span data-ttu-id="3f7de-329">**IIS (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="3f7de-329">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="3f7de-330">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-330">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="3f7de-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="3f7de-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="3f7de-333">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-333">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="3f7de-334">**IIS Express (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="3f7de-334">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="3f7de-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="3f7de-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="3f7de-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="3f7de-338">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-338">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="3f7de-339">架构</span><span class="sxs-lookup"><span data-stu-id="3f7de-339">Schema</span></span>

<span data-ttu-id="3f7de-340">**IIS**</span><span class="sxs-lookup"><span data-stu-id="3f7de-340">**IIS**</span></span>

* <span data-ttu-id="3f7de-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="3f7de-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="3f7de-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="3f7de-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="3f7de-343">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="3f7de-343">**IIS Express**</span></span>

* <span data-ttu-id="3f7de-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="3f7de-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="3f7de-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="3f7de-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="3f7de-346">配置</span><span class="sxs-lookup"><span data-stu-id="3f7de-346">Configuration</span></span>

<span data-ttu-id="3f7de-347">**IIS**</span><span class="sxs-lookup"><span data-stu-id="3f7de-347">**IIS**</span></span>

* <span data-ttu-id="3f7de-348">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="3f7de-348">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="3f7de-349">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="3f7de-349">**IIS Express**</span></span>

* <span data-ttu-id="3f7de-350">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="3f7de-350">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="3f7de-351">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="3f7de-351">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="3f7de-352">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="3f7de-352">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3f7de-353">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="3f7de-353">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="3f7de-354">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-354">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="3f7de-355">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-355">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="3f7de-356">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="3f7de-356">Supported Windows versions:</span></span>

* <span data-ttu-id="3f7de-357">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3f7de-357">Windows 7 or later</span></span>
* <span data-ttu-id="3f7de-358">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3f7de-358">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="3f7de-359">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-359">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="3f7de-360">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f7de-360">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="3f7de-361">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="3f7de-361">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="3f7de-362">托管模型</span><span class="sxs-lookup"><span data-stu-id="3f7de-362">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="3f7de-363">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="3f7de-363">In-process hosting model</span></span>

<span data-ttu-id="3f7de-364">若要配置用于进程内托管的应用，请将 `<AspNetCoreHostingModel>` 属性添加到值为 `InProcess`（进程外托管使用 `OutOfProcess` 进行设置）的应用项目文件：</span><span class="sxs-lookup"><span data-stu-id="3f7de-364">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="3f7de-365">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="3f7de-365">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="3f7de-366">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-366">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="3f7de-367">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="3f7de-367">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="3f7de-368">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="3f7de-368">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="3f7de-369">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3f7de-369">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="3f7de-370">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-370">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="3f7de-371">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-371">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="3f7de-372">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="3f7de-372">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="3f7de-373">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="3f7de-373">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="3f7de-374">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="3f7de-374">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="3f7de-375">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="3f7de-375">Use one app pool per app.</span></span>

* <span data-ttu-id="3f7de-376">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="3f7de-376">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="3f7de-377">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="3f7de-377">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="3f7de-378">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="3f7de-378">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="3f7de-379">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="3f7de-379">Client disconnects are detected.</span></span> <span data-ttu-id="3f7de-380">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="3f7de-380">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="3f7de-381">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-381">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="3f7de-382">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-382">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="3f7de-383">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="3f7de-383">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="3f7de-384">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="3f7de-384">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="3f7de-385">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="3f7de-385">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="3f7de-386">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="3f7de-386">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="3f7de-387">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="3f7de-387">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="3f7de-388">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="3f7de-388">Out-of-process hosting model</span></span>

<span data-ttu-id="3f7de-389">若要配置进程外托管应用，请在项目文件中使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="3f7de-389">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="3f7de-390">不指定 `<AspNetCoreHostingModel>` 属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-390">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="3f7de-391">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-391">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="3f7de-392">将 `<AspNetCoreHostingModel>` 属性的值设为 `OutOfProcess`（进程内托管设为 `InProcess`）：</span><span class="sxs-lookup"><span data-stu-id="3f7de-392">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="3f7de-393">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-393">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="3f7de-394">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3f7de-394">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="3f7de-395">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-395">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="3f7de-396">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="3f7de-396">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="3f7de-397">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="3f7de-397">Hosting model changes</span></span>

<span data-ttu-id="3f7de-398">如果 `hostingModel` 设置在 web.config 文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-398">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="3f7de-399">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="3f7de-399">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="3f7de-400">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-400">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="3f7de-401">进程名</span><span class="sxs-lookup"><span data-stu-id="3f7de-401">Process name</span></span>

<span data-ttu-id="3f7de-402">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-402">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="3f7de-403">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="3f7de-403">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="3f7de-404">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="3f7de-404">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="3f7de-405">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="3f7de-405">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="3f7de-406">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-406">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="3f7de-407">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="3f7de-407">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="3f7de-408">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="3f7de-408">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="3f7de-409">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="3f7de-409">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="3f7de-410">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-410">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="3f7de-411">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="3f7de-411">Configuration with web.config</span></span>

<span data-ttu-id="3f7de-412">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="3f7de-412">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="3f7de-413">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="3f7de-413">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="3f7de-414">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="3f7de-414">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="3f7de-415">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="3f7de-415">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="3f7de-416">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-416">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="3f7de-417">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-417">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="3f7de-418">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="3f7de-418">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="3f7de-419">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="3f7de-419">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="3f7de-420">特性</span><span class="sxs-lookup"><span data-stu-id="3f7de-420">Attribute</span></span> | <span data-ttu-id="3f7de-421">说明</span><span class="sxs-lookup"><span data-stu-id="3f7de-421">Description</span></span> | <span data-ttu-id="3f7de-422">默认</span><span class="sxs-lookup"><span data-stu-id="3f7de-422">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="3f7de-423">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-423">Optional string attribute.</span></span></p><p><span data-ttu-id="3f7de-424">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="3f7de-424">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="3f7de-425">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-425">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="3f7de-426">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="3f7de-426">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="3f7de-427">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-427">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="3f7de-428">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-428">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="3f7de-429">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="3f7de-429">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="3f7de-430">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-430">Optional string attribute.</span></span></p><p><span data-ttu-id="3f7de-431">将托管模型指定为进程内 (`InProcess`) 或进程外 (`OutOfProcess`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-431">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="3f7de-432">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-432">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-433">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="3f7de-433">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="3f7de-434">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-434">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="3f7de-435">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-435">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="3f7de-436">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-436">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="3f7de-437">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="3f7de-437">Default: `1`</span></span><br><span data-ttu-id="3f7de-438">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="3f7de-438">Min: `1`</span></span><br><span data-ttu-id="3f7de-439">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="3f7de-439">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="3f7de-440">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-440">Required string attribute.</span></span></p><p><span data-ttu-id="3f7de-441">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-441">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="3f7de-442">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-442">Relative paths are supported.</span></span> <span data-ttu-id="3f7de-443">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="3f7de-443">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="3f7de-444">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-444">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-445">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="3f7de-445">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="3f7de-446">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-446">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="3f7de-447">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="3f7de-447">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="3f7de-448">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="3f7de-448">Default: `10`</span></span><br><span data-ttu-id="3f7de-449">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="3f7de-449">Min: `0`</span></span><br><span data-ttu-id="3f7de-450">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="3f7de-450">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="3f7de-451">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-451">Optional timespan attribute.</span></span></p><p><span data-ttu-id="3f7de-452">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="3f7de-452">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="3f7de-453">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-453">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="3f7de-454">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="3f7de-454">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="3f7de-455">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-455">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="3f7de-456">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="3f7de-456">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="3f7de-457">在分钟或秒钟值中使用“60”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="3f7de-457">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="3f7de-458">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="3f7de-458">Default: `00:02:00`</span></span><br><span data-ttu-id="3f7de-459">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="3f7de-459">Min: `00:00:00`</span></span><br><span data-ttu-id="3f7de-460">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="3f7de-460">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="3f7de-461">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-461">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-462">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-462">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="3f7de-463">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="3f7de-463">Default: `10`</span></span><br><span data-ttu-id="3f7de-464">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="3f7de-464">Min: `0`</span></span><br><span data-ttu-id="3f7de-465">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="3f7de-465">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="3f7de-466">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-466">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-467">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-467">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="3f7de-468">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-468">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="3f7de-469">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="3f7de-469">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="3f7de-470">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="3f7de-470">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="3f7de-471">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="3f7de-471">Default: `120`</span></span><br><span data-ttu-id="3f7de-472">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="3f7de-472">Min: `0`</span></span><br><span data-ttu-id="3f7de-473">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="3f7de-473">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="3f7de-474">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-474">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="3f7de-475">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="3f7de-475">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="3f7de-476">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-476">Optional string attribute.</span></span></p><p><span data-ttu-id="3f7de-477">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-477">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="3f7de-478">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="3f7de-478">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="3f7de-479">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-479">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="3f7de-480">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="3f7de-480">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="3f7de-481">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="3f7de-481">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="3f7de-482">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="3f7de-482">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="3f7de-483">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="3f7de-483">Setting environment variables</span></span>

<span data-ttu-id="3f7de-484">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-484">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="3f7de-485">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-485">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="3f7de-486">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-486">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="3f7de-487">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-487">The following example sets two environment variables.</span></span> <span data-ttu-id="3f7de-488">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-488">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="3f7de-489">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-489">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="3f7de-490">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-490">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="3f7de-491">直接在 web.config 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml）或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="3f7de-491">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="3f7de-492">此方法在发布项目时设置 web.config 中的环境：</span><span class="sxs-lookup"><span data-stu-id="3f7de-492">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="3f7de-493">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-493">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="3f7de-494">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="3f7de-494">app_offline.htm</span></span>

<span data-ttu-id="3f7de-495">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-495">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="3f7de-496">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-496">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="3f7de-497">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-497">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="3f7de-498">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="3f7de-498">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="3f7de-499">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="3f7de-499">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="3f7de-500">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="3f7de-500">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="3f7de-501">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="3f7de-501">Start-up error page</span></span>

<span data-ttu-id="3f7de-502">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="3f7de-502">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="3f7de-503">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="3f7de-503">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="3f7de-504">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="3f7de-504">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="3f7de-505">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="3f7de-505">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="3f7de-506">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-506">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="3f7de-507">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-507">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="3f7de-508">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="3f7de-508">Log creation and redirection</span></span>

<span data-ttu-id="3f7de-509">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="3f7de-509">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="3f7de-510">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="3f7de-510">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="3f7de-511">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-511">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="3f7de-512">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-512">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="3f7de-513">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="3f7de-513">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="3f7de-514">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="3f7de-514">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="3f7de-515">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="3f7de-515">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="3f7de-516">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="3f7de-516">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f7de-517">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-517">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="3f7de-518">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="3f7de-518">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="3f7de-519">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="3f7de-519">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="3f7de-520">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="3f7de-520">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="3f7de-521">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="3f7de-521">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="3f7de-522">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="3f7de-522">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="3f7de-523">以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="3f7de-523">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="3f7de-524">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-524">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="3f7de-525">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="3f7de-525">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="3f7de-526">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="3f7de-526">Enhanced diagnostic logs</span></span>

<span data-ttu-id="3f7de-527">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="3f7de-527">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="3f7de-528">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="3f7de-528">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="3f7de-529">提供给 `<handlerSetting>` 值（上述示例中为 logs）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="3f7de-529">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="3f7de-530">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-530">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="3f7de-531">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-531">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="3f7de-532">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="3f7de-532">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="3f7de-533">错误</span><span class="sxs-lookup"><span data-stu-id="3f7de-533">ERROR</span></span>
* <span data-ttu-id="3f7de-534">警告</span><span class="sxs-lookup"><span data-stu-id="3f7de-534">WARNING</span></span>
* <span data-ttu-id="3f7de-535">信息</span><span class="sxs-lookup"><span data-stu-id="3f7de-535">INFO</span></span>
* <span data-ttu-id="3f7de-536">TRACE</span><span class="sxs-lookup"><span data-stu-id="3f7de-536">TRACE</span></span>

<span data-ttu-id="3f7de-537">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="3f7de-537">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="3f7de-538">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="3f7de-538">CONSOLE</span></span>
* <span data-ttu-id="3f7de-539">事件日志</span><span class="sxs-lookup"><span data-stu-id="3f7de-539">EVENTLOG</span></span>
* <span data-ttu-id="3f7de-540">文件</span><span class="sxs-lookup"><span data-stu-id="3f7de-540">FILE</span></span>

<span data-ttu-id="3f7de-541">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="3f7de-541">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="3f7de-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; 调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="3f7de-543">（默认值：aspnetcore debug.log）</span><span class="sxs-lookup"><span data-stu-id="3f7de-543">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="3f7de-544">`ASPNETCORE_MODULE_DEBUG` &ndash; 调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-544">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="3f7de-545">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="3f7de-545">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="3f7de-546">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="3f7de-546">The size of the log isn't limited.</span></span> <span data-ttu-id="3f7de-547">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="3f7de-547">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="3f7de-548">有关 web.config 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-548">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="3f7de-549">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="3f7de-549">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="3f7de-550">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="3f7de-550">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="3f7de-551">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="3f7de-551">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="3f7de-552">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="3f7de-552">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="3f7de-553">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="3f7de-553">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="3f7de-554">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-554">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="3f7de-555">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-555">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="3f7de-556">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="3f7de-556">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="3f7de-557">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-557">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="3f7de-558">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-558">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="3f7de-559">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-559">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="3f7de-560">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="3f7de-560">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="3f7de-561">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="3f7de-561">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="3f7de-562">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="3f7de-562">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="3f7de-563">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="3f7de-563">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="3f7de-564">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="3f7de-564">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="3f7de-565">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-565">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="3f7de-566">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="3f7de-566">Run the installer.</span></span>
1. <span data-ttu-id="3f7de-567">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="3f7de-567">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="3f7de-568">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-568">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="3f7de-569">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="3f7de-569">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="3f7de-570">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3f7de-570">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="3f7de-571">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="3f7de-571">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="3f7de-572">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="3f7de-572">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="3f7de-573">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="3f7de-573">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="3f7de-574">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="3f7de-574">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="3f7de-575">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="3f7de-575">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="3f7de-576">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="3f7de-576">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="3f7de-577">模块</span><span class="sxs-lookup"><span data-stu-id="3f7de-577">Module</span></span>

<span data-ttu-id="3f7de-578">**IIS (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="3f7de-578">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="3f7de-579">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-579">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="3f7de-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="3f7de-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="3f7de-582">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-582">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="3f7de-583">**IIS Express (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="3f7de-583">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="3f7de-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="3f7de-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="3f7de-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="3f7de-587">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-587">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="3f7de-588">架构</span><span class="sxs-lookup"><span data-stu-id="3f7de-588">Schema</span></span>

<span data-ttu-id="3f7de-589">**IIS**</span><span class="sxs-lookup"><span data-stu-id="3f7de-589">**IIS**</span></span>

* <span data-ttu-id="3f7de-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="3f7de-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="3f7de-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="3f7de-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="3f7de-592">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="3f7de-592">**IIS Express**</span></span>

* <span data-ttu-id="3f7de-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="3f7de-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="3f7de-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="3f7de-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="3f7de-595">配置</span><span class="sxs-lookup"><span data-stu-id="3f7de-595">Configuration</span></span>

<span data-ttu-id="3f7de-596">**IIS**</span><span class="sxs-lookup"><span data-stu-id="3f7de-596">**IIS**</span></span>

* <span data-ttu-id="3f7de-597">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="3f7de-597">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="3f7de-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="3f7de-598">**IIS Express**</span></span>

* <span data-ttu-id="3f7de-599">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="3f7de-599">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="3f7de-600">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="3f7de-600">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="3f7de-601">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="3f7de-601">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3f7de-602">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于将 Web 请求转发到后端 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="3f7de-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="3f7de-603">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="3f7de-603">Supported Windows versions:</span></span>

* <span data-ttu-id="3f7de-604">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3f7de-604">Windows 7 or later</span></span>
* <span data-ttu-id="3f7de-605">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3f7de-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="3f7de-606">该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f7de-606">The module only works with Kestrel.</span></span> <span data-ttu-id="3f7de-607">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="3f7de-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="3f7de-608">由于 ASP.NET Core 应用在独立于 IIS 辅助进程的进程中运行，因此该模块还处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="3f7de-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="3f7de-609">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="3f7de-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="3f7de-610">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的 IIS 中在进程内运行的 ASP.NET 4.x 应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="3f7de-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="3f7de-611">下图说明了 IIS、ASP.NET Core 模块和应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="3f7de-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模块](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="3f7de-613">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="3f7de-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="3f7de-614">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="3f7de-615">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f7de-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="3f7de-616">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="3f7de-617">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="3f7de-618">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="3f7de-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="3f7de-619">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="3f7de-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="3f7de-620">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="3f7de-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="3f7de-621">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f7de-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="3f7de-622">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="3f7de-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="3f7de-623">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="3f7de-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="3f7de-624">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="3f7de-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="3f7de-625">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="3f7de-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="3f7de-626">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="3f7de-627">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="3f7de-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="3f7de-628">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="3f7de-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="3f7de-629">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="3f7de-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="3f7de-630">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="3f7de-631">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="3f7de-631">Configuration with web.config</span></span>

<span data-ttu-id="3f7de-632">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="3f7de-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="3f7de-633">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="3f7de-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="3f7de-634">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="3f7de-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="3f7de-635">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="3f7de-636">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="3f7de-637">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="3f7de-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="3f7de-638">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="3f7de-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="3f7de-639">特性</span><span class="sxs-lookup"><span data-stu-id="3f7de-639">Attribute</span></span> | <span data-ttu-id="3f7de-640">说明</span><span class="sxs-lookup"><span data-stu-id="3f7de-640">Description</span></span> | <span data-ttu-id="3f7de-641">默认</span><span class="sxs-lookup"><span data-stu-id="3f7de-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="3f7de-642">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-642">Optional string attribute.</span></span></p><p><span data-ttu-id="3f7de-643">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="3f7de-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="3f7de-644">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="3f7de-645">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="3f7de-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="3f7de-646">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="3f7de-647">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="3f7de-648">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="3f7de-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="3f7de-649">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-650">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="3f7de-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="3f7de-651">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="3f7de-652">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="3f7de-653">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="3f7de-653">Default: `1`</span></span><br><span data-ttu-id="3f7de-654">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="3f7de-654">Min: `1`</span></span><br><span data-ttu-id="3f7de-655">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="3f7de-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="3f7de-656">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-656">Required string attribute.</span></span></p><p><span data-ttu-id="3f7de-657">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="3f7de-658">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-658">Relative paths are supported.</span></span> <span data-ttu-id="3f7de-659">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="3f7de-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="3f7de-660">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-661">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="3f7de-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="3f7de-662">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="3f7de-663">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="3f7de-663">Default: `10`</span></span><br><span data-ttu-id="3f7de-664">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="3f7de-664">Min: `0`</span></span><br><span data-ttu-id="3f7de-665">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="3f7de-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="3f7de-666">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="3f7de-667">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="3f7de-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="3f7de-668">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="3f7de-669">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="3f7de-669">Default: `00:02:00`</span></span><br><span data-ttu-id="3f7de-670">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="3f7de-670">Min: `00:00:00`</span></span><br><span data-ttu-id="3f7de-671">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="3f7de-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="3f7de-672">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-673">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="3f7de-674">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="3f7de-674">Default: `10`</span></span><br><span data-ttu-id="3f7de-675">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="3f7de-675">Min: `0`</span></span><br><span data-ttu-id="3f7de-676">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="3f7de-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="3f7de-677">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="3f7de-678">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="3f7de-679">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="3f7de-680">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="3f7de-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="3f7de-681">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="3f7de-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="3f7de-682">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="3f7de-682">Default: `120`</span></span><br><span data-ttu-id="3f7de-683">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="3f7de-683">Min: `0`</span></span><br><span data-ttu-id="3f7de-684">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="3f7de-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="3f7de-685">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="3f7de-686">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="3f7de-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="3f7de-687">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-687">Optional string attribute.</span></span></p><p><span data-ttu-id="3f7de-688">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="3f7de-689">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="3f7de-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="3f7de-690">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="3f7de-691">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="3f7de-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="3f7de-692">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="3f7de-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="3f7de-693">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="3f7de-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="3f7de-694">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="3f7de-694">Setting environment variables</span></span>

<span data-ttu-id="3f7de-695">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="3f7de-696">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="3f7de-697">本部分中设置的环境变量与使用同一名称设置的系统环境变量冲突。</span><span class="sxs-lookup"><span data-stu-id="3f7de-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="3f7de-698">如果在 web.config 文件以及 Windows 中的系统级别中同时设置了环境变量，则 web.config 文件中的值将被追加到系统环境变量值（例如，`ASPNETCORE_ENVIRONMENT: Development;Development`），这将阻止应用启动。</span><span class="sxs-lookup"><span data-stu-id="3f7de-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="3f7de-699">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-699">The following example sets two environment variables.</span></span> <span data-ttu-id="3f7de-700">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="3f7de-701">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="3f7de-702">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="3f7de-703">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="3f7de-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="3f7de-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="3f7de-704">app_offline.htm</span></span>

<span data-ttu-id="3f7de-705">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="3f7de-706">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="3f7de-707">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="3f7de-708">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="3f7de-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="3f7de-709">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="3f7de-709">Start-up error page</span></span>

<span data-ttu-id="3f7de-710">如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="3f7de-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="3f7de-711">若要禁止显示此页面并还原为默认 IIS 502 状态代码页面，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="3f7de-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="3f7de-712">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![“502.5 进程失败”状态代码页面](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="3f7de-714">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="3f7de-714">Log creation and redirection</span></span>

<span data-ttu-id="3f7de-715">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="3f7de-715">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="3f7de-716">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="3f7de-716">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="3f7de-717">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-717">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="3f7de-718">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="3f7de-718">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="3f7de-719">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="3f7de-719">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="3f7de-720">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="3f7de-720">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="3f7de-721">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="3f7de-721">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="3f7de-722">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="3f7de-722">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f7de-723">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-723">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="3f7de-724">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="3f7de-724">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="3f7de-725">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="3f7de-725">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="3f7de-726">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="3f7de-726">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="3f7de-727">以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="3f7de-727">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="3f7de-728">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="3f7de-728">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="3f7de-729">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="3f7de-729">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

<span data-ttu-id="3f7de-730">提供给 `<handlerSetting>` 值（上述示例中为 logs）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="3f7de-730">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="3f7de-731">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="3f7de-731">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="3f7de-732">有关 web.config 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-732">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="3f7de-733">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="3f7de-733">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="3f7de-734">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="3f7de-734">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="3f7de-735">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="3f7de-735">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="3f7de-736">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="3f7de-736">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="3f7de-737">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-737">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="3f7de-738">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="3f7de-738">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="3f7de-739">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="3f7de-739">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="3f7de-740">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-740">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="3f7de-741">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="3f7de-741">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="3f7de-742">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="3f7de-742">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="3f7de-743">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="3f7de-743">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="3f7de-744">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="3f7de-744">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="3f7de-745">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="3f7de-745">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="3f7de-746">使用 IIS 共享配置时，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="3f7de-746">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="3f7de-747">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-747">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="3f7de-748">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="3f7de-748">Run the installer.</span></span>
1. <span data-ttu-id="3f7de-749">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="3f7de-749">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="3f7de-750">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="3f7de-750">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="3f7de-751">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="3f7de-751">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="3f7de-752">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3f7de-752">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="3f7de-753">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="3f7de-753">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="3f7de-754">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="3f7de-754">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="3f7de-755">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="3f7de-755">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="3f7de-756">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="3f7de-756">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="3f7de-757">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="3f7de-757">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="3f7de-758">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="3f7de-758">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="3f7de-759">模块</span><span class="sxs-lookup"><span data-stu-id="3f7de-759">Module</span></span>

<span data-ttu-id="3f7de-760">**IIS (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="3f7de-760">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="3f7de-761">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-761">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="3f7de-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="3f7de-763">**IIS Express (x86/amd64)**：</span><span class="sxs-lookup"><span data-stu-id="3f7de-763">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="3f7de-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="3f7de-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="3f7de-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="3f7de-766">架构</span><span class="sxs-lookup"><span data-stu-id="3f7de-766">Schema</span></span>

<span data-ttu-id="3f7de-767">**IIS**</span><span class="sxs-lookup"><span data-stu-id="3f7de-767">**IIS**</span></span>

* <span data-ttu-id="3f7de-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="3f7de-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="3f7de-769">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="3f7de-769">**IIS Express**</span></span>

* <span data-ttu-id="3f7de-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="3f7de-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="3f7de-771">配置</span><span class="sxs-lookup"><span data-stu-id="3f7de-771">Configuration</span></span>

<span data-ttu-id="3f7de-772">**IIS**</span><span class="sxs-lookup"><span data-stu-id="3f7de-772">**IIS**</span></span>

* <span data-ttu-id="3f7de-773">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="3f7de-773">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="3f7de-774">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="3f7de-774">**IIS Express**</span></span>

* <span data-ttu-id="3f7de-775">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="3f7de-775">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="3f7de-776">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="3f7de-776">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="3f7de-777">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="3f7de-777">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3f7de-778">其他资源</span><span class="sxs-lookup"><span data-stu-id="3f7de-778">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="3f7de-779">ASP.NET Core 模块 GitHub 存储库（引用源）</span><span class="sxs-lookup"><span data-stu-id="3f7de-779">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
