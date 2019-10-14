---
title: ASP.NET Core 模块
author: guardrex
description: 了解如何配置 ASP.NET Core 模块以托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: c1c34f368cb3f7767bf0f229ff70c5ab53c6005f
ms.sourcegitcommit: fcdf9aaa6c45c1a926bd870ed8f893bdb4935152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165324"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="8091c-103">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8091c-103">ASP.NET Core Module</span></span>

<span data-ttu-id="8091c-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8091c-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8091c-105">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="8091c-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="8091c-106">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="8091c-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="8091c-107">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="8091c-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="8091c-108">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="8091c-108">Supported Windows versions:</span></span>

* <span data-ttu-id="8091c-109">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8091c-109">Windows 7 or later</span></span>
* <span data-ttu-id="8091c-110">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8091c-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="8091c-111">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="8091c-112">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="8091c-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="8091c-113">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="8091c-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="8091c-114">托管模型</span><span class="sxs-lookup"><span data-stu-id="8091c-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="8091c-115">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="8091c-115">In-process hosting model</span></span>

<span data-ttu-id="8091c-116">ASP.NET Core 应用默认为进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="8091c-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="8091c-117">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="8091c-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="8091c-118">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="8091c-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="8091c-119">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8091c-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="8091c-120">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="8091c-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="8091c-121">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="8091c-122">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="8091c-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="8091c-123">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8091c-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="8091c-124">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="8091c-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="8091c-125">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="8091c-125">Use one app pool per app.</span></span>

* <span data-ttu-id="8091c-126">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="8091c-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="8091c-127">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="8091c-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="8091c-128">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="8091c-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="8091c-129">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="8091c-129">Client disconnects are detected.</span></span> <span data-ttu-id="8091c-130">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="8091c-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="8091c-131">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv   ）。</span><span class="sxs-lookup"><span data-stu-id="8091c-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="8091c-132">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="8091c-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="8091c-133">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="8091c-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="8091c-134">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="8091c-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="8091c-135">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="8091c-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="8091c-136">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="8091c-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="8091c-137">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="8091c-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="8091c-138">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="8091c-138">Out-of-process hosting model</span></span>

<span data-ttu-id="8091c-139">若要配置进程外托管应用，请在项目文件 ( *.csproj*) 中将 `<AspNetCoreHostingModel>` 属性的值设置为 `OutOfProcess`：</span><span class="sxs-lookup"><span data-stu-id="8091c-139">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="8091c-140">进程内托管设为 `InProcess`，这是默认值。</span><span class="sxs-lookup"><span data-stu-id="8091c-140">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="8091c-141">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-141">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="8091c-142">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8091c-142">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="8091c-143">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-143">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="8091c-144">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="8091c-144">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="8091c-145">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="8091c-145">Hosting model changes</span></span>

<span data-ttu-id="8091c-146">如果 `hostingModel` 设置在 web.config  文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-146">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="8091c-147">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8091c-147">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="8091c-148">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-148">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="8091c-149">进程名</span><span class="sxs-lookup"><span data-stu-id="8091c-149">Process name</span></span>

<span data-ttu-id="8091c-150">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="8091c-150">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="8091c-151">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="8091c-151">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="8091c-152">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="8091c-152">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="8091c-153">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="8091c-153">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="8091c-154">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-154">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="8091c-155">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="8091c-155">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="8091c-156">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="8091c-156">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="8091c-157">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8091c-157">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="8091c-158">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="8091c-158">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="8091c-159">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="8091c-159">Configuration with web.config</span></span>

<span data-ttu-id="8091c-160">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="8091c-160">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="8091c-161">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="8091c-161">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="8091c-162">以下 web.config  发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8091c-162">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="8091c-163">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="8091c-163">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="8091c-164">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8091c-164">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="8091c-165">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="8091c-165">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="8091c-166">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="8091c-166">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="8091c-167">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="8091c-167">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="8091c-168">特性</span><span class="sxs-lookup"><span data-stu-id="8091c-168">Attribute</span></span> | <span data-ttu-id="8091c-169">说明</span><span class="sxs-lookup"><span data-stu-id="8091c-169">Description</span></span> | <span data-ttu-id="8091c-170">默认</span><span class="sxs-lookup"><span data-stu-id="8091c-170">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="8091c-171">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-171">Optional string attribute.</span></span></p><p><span data-ttu-id="8091c-172">processPath  中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="8091c-172">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="8091c-173">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-173">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8091c-174">如果为 true，将禁止显示“502.5 - 进程失败”  页面，而会优先显示 web.config  中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="8091c-174">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="8091c-175">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8091c-176">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-176">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="8091c-177">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="8091c-177">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="8091c-178">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-178">Optional string attribute.</span></span></p><p><span data-ttu-id="8091c-179">将托管模型指定为进程内 (`InProcess`) 或进程外 (`OutOfProcess`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-179">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `InProcess` |
| `processesPerApplication` | <p><span data-ttu-id="8091c-180">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-180">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-181">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="8091c-181">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="8091c-182">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="8091c-182">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="8091c-183">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="8091c-183">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="8091c-184">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-184">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="8091c-185">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="8091c-185">Default: `1`</span></span><br><span data-ttu-id="8091c-186">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="8091c-186">Min: `1`</span></span><br><span data-ttu-id="8091c-187">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="8091c-187">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="8091c-188">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-188">Required string attribute.</span></span></p><p><span data-ttu-id="8091c-189">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-189">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="8091c-190">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-190">Relative paths are supported.</span></span> <span data-ttu-id="8091c-191">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8091c-191">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="8091c-192">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-192">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-193">指定允许 processPath  中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="8091c-193">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="8091c-194">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-194">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="8091c-195">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8091c-195">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="8091c-196">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8091c-196">Default: `10`</span></span><br><span data-ttu-id="8091c-197">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8091c-197">Min: `0`</span></span><br><span data-ttu-id="8091c-198">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="8091c-198">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="8091c-199">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-199">Optional timespan attribute.</span></span></p><p><span data-ttu-id="8091c-200">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="8091c-200">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="8091c-201">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="8091c-201">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="8091c-202">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8091c-202">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="8091c-203">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-203">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="8091c-204">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="8091c-204">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="8091c-205">在分钟或秒钟值中使用“60”  会导致“500 - 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="8091c-205">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="8091c-206">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="8091c-206">Default: `00:02:00`</span></span><br><span data-ttu-id="8091c-207">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="8091c-207">Min: `00:00:00`</span></span><br><span data-ttu-id="8091c-208">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="8091c-208">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="8091c-209">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-209">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-210">检测到 app_offline.htm  文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8091c-210">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="8091c-211">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8091c-211">Default: `10`</span></span><br><span data-ttu-id="8091c-212">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8091c-212">Min: `0`</span></span><br><span data-ttu-id="8091c-213">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="8091c-213">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="8091c-214">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-214">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-215">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8091c-215">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="8091c-216">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-216">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="8091c-217">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute  次。</span><span class="sxs-lookup"><span data-stu-id="8091c-217">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="8091c-218">值 0（零）  不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="8091c-218">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="8091c-219">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="8091c-219">Default: `120`</span></span><br><span data-ttu-id="8091c-220">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8091c-220">Min: `0`</span></span><br><span data-ttu-id="8091c-221">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="8091c-221">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="8091c-222">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-222">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8091c-223">如果为 true，processPath  中指定的 进程的 stdout  和 stderr  将重定向到 stdoutLogFile  中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="8091c-223">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="8091c-224">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-224">Optional string attribute.</span></span></p><p><span data-ttu-id="8091c-225">指定在其中记录 processPath  中指定进程的 stdout  和 stderr  的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-225">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="8091c-226">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8091c-226">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="8091c-227">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-227">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="8091c-228">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="8091c-228">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="8091c-229">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log  ) 添加到 stdoutLogFile  路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="8091c-229">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="8091c-230">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs  文件夹中保存为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8091c-230">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="8091c-231">设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-231">Set environment variables</span></span>

<span data-ttu-id="8091c-232">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-232">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="8091c-233">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-233">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="8091c-234">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-234">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="8091c-235">以下示例在 web.config  中设置两个环境变量。`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8091c-235">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="8091c-236">开发人员可能会暂时在 web.config  文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="8091c-236">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="8091c-237">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-237">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="8091c-238">直接在 web.config  中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml  ）或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="8091c-238">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="8091c-239">此方法在发布项目时设置 web.config  中的环境：</span><span class="sxs-lookup"><span data-stu-id="8091c-239">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="8091c-240">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8091c-240">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="8091c-241">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="8091c-241">app_offline.htm</span></span>

<span data-ttu-id="8091c-242">如果在应用的根目录中检测到名为 “app_offline.htm”  的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-242">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="8091c-243">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-243">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="8091c-244">存在 app_offline.htm  文件时，ASP.NET Core 模块会通过发送回 app_offline.htm  文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-244">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="8091c-245">删除 app_offline.htm  文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="8091c-245">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="8091c-246">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="8091c-246">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="8091c-247">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="8091c-247">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="8091c-248">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="8091c-248">Start-up error page</span></span>

<span data-ttu-id="8091c-249">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="8091c-249">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="8091c-250">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="8091c-250">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="8091c-251">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="8091c-251">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="8091c-252">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”  状态代码页。</span><span class="sxs-lookup"><span data-stu-id="8091c-252">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="8091c-253">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-253">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="8091c-254">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="8091c-254">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="8091c-255">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="8091c-255">Log creation and redirection</span></span>

<span data-ttu-id="8091c-256">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="8091c-256">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="8091c-257">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="8091c-257">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="8091c-258">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8091c-258">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8091c-259">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-259">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="8091c-260">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="8091c-260">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="8091c-261">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="8091c-261">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="8091c-262">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="8091c-262">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="8091c-263">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8091c-263">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8091c-264">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8091c-264">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="8091c-265">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="8091c-265">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="8091c-266">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log  ) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout  ）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="8091c-266">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="8091c-267">如果 `stdoutLogFile` 路径以 stdout  结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8091c-267">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="8091c-268">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="8091c-268">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="8091c-269">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="8091c-269">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="8091c-270">web.config 文件中的以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录  。</span><span class="sxs-lookup"><span data-stu-id="8091c-270">The following sample `aspNetCore` element in a *web.config* file configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="8091c-271">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-271">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="8091c-272">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="8091c-272">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="8091c-273">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="8091c-273">Enhanced diagnostic logs</span></span>

<span data-ttu-id="8091c-274">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="8091c-274">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="8091c-275">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素  。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="8091c-275">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="8091c-276">创建日志文件时，路径中的任何文件夹（上述示例中为 logs  ）由模块创建。</span><span class="sxs-lookup"><span data-stu-id="8091c-276">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="8091c-277">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8091c-277">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8091c-278">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="8091c-278">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="8091c-279">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="8091c-279">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="8091c-280">错误</span><span class="sxs-lookup"><span data-stu-id="8091c-280">ERROR</span></span>
* <span data-ttu-id="8091c-281">警告</span><span class="sxs-lookup"><span data-stu-id="8091c-281">WARNING</span></span>
* <span data-ttu-id="8091c-282">信息</span><span class="sxs-lookup"><span data-stu-id="8091c-282">INFO</span></span>
* <span data-ttu-id="8091c-283">TRACE</span><span class="sxs-lookup"><span data-stu-id="8091c-283">TRACE</span></span>

<span data-ttu-id="8091c-284">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="8091c-284">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="8091c-285">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="8091c-285">CONSOLE</span></span>
* <span data-ttu-id="8091c-286">事件日志</span><span class="sxs-lookup"><span data-stu-id="8091c-286">EVENTLOG</span></span>
* <span data-ttu-id="8091c-287">文件</span><span class="sxs-lookup"><span data-stu-id="8091c-287">FILE</span></span>

<span data-ttu-id="8091c-288">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="8091c-288">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="8091c-289">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; 调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-289">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="8091c-290">（默认值：aspnetcore debug.log） </span><span class="sxs-lookup"><span data-stu-id="8091c-290">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="8091c-291">`ASPNETCORE_MODULE_DEBUG` &ndash; 调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="8091c-291">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="8091c-292">部署中启用的调试日志记录的时间不要超出排除故障所需的时间  。</span><span class="sxs-lookup"><span data-stu-id="8091c-292">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="8091c-293">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="8091c-293">The size of the log isn't limited.</span></span> <span data-ttu-id="8091c-294">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="8091c-294">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="8091c-295">有关 web.config  文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="8091c-295">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="8091c-296">修改堆栈大小</span><span class="sxs-lookup"><span data-stu-id="8091c-296">Modify the stack size</span></span>

<span data-ttu-id="8091c-297">*仅适用于使用进程内托管模型的情况。*</span><span class="sxs-lookup"><span data-stu-id="8091c-297">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="8091c-298">使用 web.config 中的 `stackSize` 设置（以字节为单位）配置托管堆栈大小  。默认大小为 `1048576` 个字节 (1 MB)。</span><span class="sxs-lookup"><span data-stu-id="8091c-298">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="8091c-299">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="8091c-299">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="8091c-300">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="8091c-300">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="8091c-301">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="8091c-301">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="8091c-302">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="8091c-302">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="8091c-303">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="8091c-303">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="8091c-304">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-304">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="8091c-305">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-305">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="8091c-306">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="8091c-306">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="8091c-307">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-307">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="8091c-308">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="8091c-308">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="8091c-309">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-309">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="8091c-310">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8091c-310">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="8091c-311">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行  。</span><span class="sxs-lookup"><span data-stu-id="8091c-311">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="8091c-312">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误  。</span><span class="sxs-lookup"><span data-stu-id="8091c-312">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="8091c-313">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="8091c-313">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="8091c-314">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="8091c-314">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="8091c-315">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8091c-315">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="8091c-316">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="8091c-316">Run the installer.</span></span>
1. <span data-ttu-id="8091c-317">将已更新的 applicationHost.config  文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="8091c-317">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="8091c-318">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8091c-318">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="8091c-319">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="8091c-319">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="8091c-320">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8091c-320">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="8091c-321">在托管系统上，导航到 %windir%\System32\inetsrv  。</span><span class="sxs-lookup"><span data-stu-id="8091c-321">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="8091c-322">找到 aspnetcore.dll  文件。</span><span class="sxs-lookup"><span data-stu-id="8091c-322">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="8091c-323">右键单击该文件，然后从上下文菜单中选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="8091c-323">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="8091c-324">选择“详细信息”选项卡  。“文件版本”  和“产品版本”  表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="8091c-324">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="8091c-325">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp  中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log  。</span><span class="sxs-lookup"><span data-stu-id="8091c-325">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="8091c-326">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="8091c-326">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="8091c-327">模块</span><span class="sxs-lookup"><span data-stu-id="8091c-327">Module</span></span>

<span data-ttu-id="8091c-328">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8091c-328">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="8091c-329">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-329">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="8091c-330">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-330">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="8091c-331">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-331">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="8091c-332">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-332">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="8091c-333">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8091c-333">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="8091c-334">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-334">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="8091c-335">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-335">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="8091c-336">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-336">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="8091c-337">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-337">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="8091c-338">架构</span><span class="sxs-lookup"><span data-stu-id="8091c-338">Schema</span></span>

<span data-ttu-id="8091c-339">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8091c-339">**IIS**</span></span>

* <span data-ttu-id="8091c-340">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8091c-340">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="8091c-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="8091c-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="8091c-342">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8091c-342">**IIS Express**</span></span>

* <span data-ttu-id="8091c-343">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8091c-343">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="8091c-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="8091c-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="8091c-345">配置</span><span class="sxs-lookup"><span data-stu-id="8091c-345">Configuration</span></span>

<span data-ttu-id="8091c-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8091c-346">**IIS**</span></span>

* <span data-ttu-id="8091c-347">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8091c-347">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="8091c-348">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8091c-348">**IIS Express**</span></span>

* <span data-ttu-id="8091c-349">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8091c-349">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="8091c-350"> iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="8091c-350">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="8091c-351">可以通过在 applicationHost.config  文件中搜索 aspnetcore  找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="8091c-351">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="8091c-352">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="8091c-352">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="8091c-353">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="8091c-353">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="8091c-354">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="8091c-354">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="8091c-355">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="8091c-355">Supported Windows versions:</span></span>

* <span data-ttu-id="8091c-356">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8091c-356">Windows 7 or later</span></span>
* <span data-ttu-id="8091c-357">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8091c-357">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="8091c-358">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-358">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="8091c-359">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="8091c-359">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="8091c-360">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="8091c-360">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="8091c-361">托管模型</span><span class="sxs-lookup"><span data-stu-id="8091c-361">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="8091c-362">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="8091c-362">In-process hosting model</span></span>

<span data-ttu-id="8091c-363">若要配置用于进程内托管的应用，请将 `<AspNetCoreHostingModel>` 属性添加到值为 `InProcess`（进程外托管使用 `OutOfProcess` 进行设置）的应用项目文件：</span><span class="sxs-lookup"><span data-stu-id="8091c-363">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="8091c-364">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="8091c-364">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="8091c-365">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="8091c-365">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="8091c-366">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="8091c-366">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="8091c-367">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="8091c-367">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="8091c-368">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8091c-368">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="8091c-369">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="8091c-369">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="8091c-370">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-370">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="8091c-371">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="8091c-371">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="8091c-372">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8091c-372">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="8091c-373">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="8091c-373">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="8091c-374">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="8091c-374">Use one app pool per app.</span></span>

* <span data-ttu-id="8091c-375">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="8091c-375">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="8091c-376">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="8091c-376">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="8091c-377">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="8091c-377">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="8091c-378">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="8091c-378">Client disconnects are detected.</span></span> <span data-ttu-id="8091c-379">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="8091c-379">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="8091c-380">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv   ）。</span><span class="sxs-lookup"><span data-stu-id="8091c-380">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="8091c-381">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="8091c-381">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="8091c-382">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="8091c-382">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="8091c-383">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="8091c-383">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="8091c-384">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="8091c-384">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="8091c-385">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="8091c-385">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="8091c-386">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="8091c-386">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="8091c-387">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="8091c-387">Out-of-process hosting model</span></span>

<span data-ttu-id="8091c-388">若要配置进程外托管应用，请在项目文件中使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="8091c-388">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="8091c-389">不指定 `<AspNetCoreHostingModel>` 属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-389">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="8091c-390">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="8091c-390">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="8091c-391">将 `<AspNetCoreHostingModel>` 属性的值设为 `OutOfProcess`（进程内托管设为 `InProcess`）：</span><span class="sxs-lookup"><span data-stu-id="8091c-391">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="8091c-392">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-392">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="8091c-393">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8091c-393">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="8091c-394">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-394">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="8091c-395">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="8091c-395">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="8091c-396">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="8091c-396">Hosting model changes</span></span>

<span data-ttu-id="8091c-397">如果 `hostingModel` 设置在 web.config  文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-397">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="8091c-398">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8091c-398">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="8091c-399">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-399">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="8091c-400">进程名</span><span class="sxs-lookup"><span data-stu-id="8091c-400">Process name</span></span>

<span data-ttu-id="8091c-401">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="8091c-401">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="8091c-402">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="8091c-402">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="8091c-403">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="8091c-403">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="8091c-404">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="8091c-404">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="8091c-405">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-405">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="8091c-406">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="8091c-406">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="8091c-407">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="8091c-407">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="8091c-408">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8091c-408">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="8091c-409">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="8091c-409">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="8091c-410">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="8091c-410">Configuration with web.config</span></span>

<span data-ttu-id="8091c-411">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="8091c-411">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="8091c-412">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="8091c-412">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="8091c-413">以下 web.config  发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8091c-413">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="8091c-414">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="8091c-414">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="8091c-415">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8091c-415">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="8091c-416">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="8091c-416">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="8091c-417">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="8091c-417">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="8091c-418">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="8091c-418">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="8091c-419">特性</span><span class="sxs-lookup"><span data-stu-id="8091c-419">Attribute</span></span> | <span data-ttu-id="8091c-420">说明</span><span class="sxs-lookup"><span data-stu-id="8091c-420">Description</span></span> | <span data-ttu-id="8091c-421">默认</span><span class="sxs-lookup"><span data-stu-id="8091c-421">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="8091c-422">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-422">Optional string attribute.</span></span></p><p><span data-ttu-id="8091c-423">processPath  中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="8091c-423">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="8091c-424">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-424">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8091c-425">如果为 true，将禁止显示“502.5 - 进程失败”  页面，而会优先显示 web.config  中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="8091c-425">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="8091c-426">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-426">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8091c-427">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-427">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="8091c-428">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="8091c-428">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="8091c-429">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-429">Optional string attribute.</span></span></p><p><span data-ttu-id="8091c-430">将托管模型指定为进程内 (`InProcess`) 或进程外 (`OutOfProcess`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-430">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="8091c-431">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-431">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-432">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="8091c-432">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="8091c-433">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="8091c-433">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="8091c-434">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="8091c-434">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="8091c-435">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-435">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="8091c-436">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="8091c-436">Default: `1`</span></span><br><span data-ttu-id="8091c-437">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="8091c-437">Min: `1`</span></span><br><span data-ttu-id="8091c-438">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="8091c-438">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="8091c-439">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-439">Required string attribute.</span></span></p><p><span data-ttu-id="8091c-440">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-440">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="8091c-441">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-441">Relative paths are supported.</span></span> <span data-ttu-id="8091c-442">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8091c-442">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="8091c-443">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-443">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-444">指定允许 processPath  中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="8091c-444">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="8091c-445">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-445">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="8091c-446">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8091c-446">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="8091c-447">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8091c-447">Default: `10`</span></span><br><span data-ttu-id="8091c-448">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8091c-448">Min: `0`</span></span><br><span data-ttu-id="8091c-449">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="8091c-449">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="8091c-450">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-450">Optional timespan attribute.</span></span></p><p><span data-ttu-id="8091c-451">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="8091c-451">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="8091c-452">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="8091c-452">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="8091c-453">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8091c-453">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="8091c-454">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-454">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="8091c-455">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="8091c-455">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="8091c-456">在分钟或秒钟值中使用“60”  会导致“500 - 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="8091c-456">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="8091c-457">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="8091c-457">Default: `00:02:00`</span></span><br><span data-ttu-id="8091c-458">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="8091c-458">Min: `00:00:00`</span></span><br><span data-ttu-id="8091c-459">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="8091c-459">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="8091c-460">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-460">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-461">检测到 app_offline.htm  文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8091c-461">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="8091c-462">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8091c-462">Default: `10`</span></span><br><span data-ttu-id="8091c-463">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8091c-463">Min: `0`</span></span><br><span data-ttu-id="8091c-464">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="8091c-464">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="8091c-465">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-465">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-466">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8091c-466">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="8091c-467">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-467">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="8091c-468">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute  次。</span><span class="sxs-lookup"><span data-stu-id="8091c-468">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="8091c-469">值 0（零）  不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="8091c-469">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="8091c-470">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="8091c-470">Default: `120`</span></span><br><span data-ttu-id="8091c-471">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8091c-471">Min: `0`</span></span><br><span data-ttu-id="8091c-472">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="8091c-472">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="8091c-473">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-473">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8091c-474">如果为 true，processPath  中指定的 进程的 stdout  和 stderr  将重定向到 stdoutLogFile  中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="8091c-474">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="8091c-475">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-475">Optional string attribute.</span></span></p><p><span data-ttu-id="8091c-476">指定在其中记录 processPath  中指定进程的 stdout  和 stderr  的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-476">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="8091c-477">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8091c-477">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="8091c-478">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-478">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="8091c-479">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="8091c-479">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="8091c-480">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log  ) 添加到 stdoutLogFile  路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="8091c-480">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="8091c-481">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs  文件夹中保存为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8091c-481">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="8091c-482">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="8091c-482">Setting environment variables</span></span>

<span data-ttu-id="8091c-483">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-483">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="8091c-484">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-484">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="8091c-485">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-485">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="8091c-486">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-486">The following example sets two environment variables.</span></span> <span data-ttu-id="8091c-487">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8091c-487">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="8091c-488">开发人员可能会暂时在 web.config  文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="8091c-488">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="8091c-489">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-489">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="8091c-490">直接在 web.config  中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml  ）或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="8091c-490">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="8091c-491">此方法在发布项目时设置 web.config  中的环境：</span><span class="sxs-lookup"><span data-stu-id="8091c-491">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="8091c-492">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8091c-492">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="8091c-493">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="8091c-493">app_offline.htm</span></span>

<span data-ttu-id="8091c-494">如果在应用的根目录中检测到名为 “app_offline.htm”  的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-494">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="8091c-495">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-495">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="8091c-496">存在 app_offline.htm  文件时，ASP.NET Core 模块会通过发送回 app_offline.htm  文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-496">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="8091c-497">删除 app_offline.htm  文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="8091c-497">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="8091c-498">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="8091c-498">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="8091c-499">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="8091c-499">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="8091c-500">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="8091c-500">Start-up error page</span></span>

<span data-ttu-id="8091c-501">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="8091c-501">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="8091c-502">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="8091c-502">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="8091c-503">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="8091c-503">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="8091c-504">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”  状态代码页。</span><span class="sxs-lookup"><span data-stu-id="8091c-504">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="8091c-505">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-505">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="8091c-506">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="8091c-506">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="8091c-507">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="8091c-507">Log creation and redirection</span></span>

<span data-ttu-id="8091c-508">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="8091c-508">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="8091c-509">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="8091c-509">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="8091c-510">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8091c-510">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8091c-511">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-511">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="8091c-512">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="8091c-512">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="8091c-513">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="8091c-513">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="8091c-514">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="8091c-514">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="8091c-515">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8091c-515">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8091c-516">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8091c-516">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="8091c-517">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="8091c-517">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="8091c-518">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log  ) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout  ）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="8091c-518">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="8091c-519">如果 `stdoutLogFile` 路径以 stdout  结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8091c-519">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="8091c-520">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="8091c-520">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="8091c-521">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="8091c-521">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="8091c-522">以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="8091c-522">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="8091c-523">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-523">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="8091c-524">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="8091c-524">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="8091c-525">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="8091c-525">Enhanced diagnostic logs</span></span>

<span data-ttu-id="8091c-526">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="8091c-526">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="8091c-527">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素  。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="8091c-527">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="8091c-528">提供给 `<handlerSetting>` 值（上述示例中为 logs  ）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="8091c-528">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="8091c-529">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8091c-529">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8091c-530">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="8091c-530">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="8091c-531">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="8091c-531">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="8091c-532">错误</span><span class="sxs-lookup"><span data-stu-id="8091c-532">ERROR</span></span>
* <span data-ttu-id="8091c-533">警告</span><span class="sxs-lookup"><span data-stu-id="8091c-533">WARNING</span></span>
* <span data-ttu-id="8091c-534">信息</span><span class="sxs-lookup"><span data-stu-id="8091c-534">INFO</span></span>
* <span data-ttu-id="8091c-535">TRACE</span><span class="sxs-lookup"><span data-stu-id="8091c-535">TRACE</span></span>

<span data-ttu-id="8091c-536">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="8091c-536">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="8091c-537">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="8091c-537">CONSOLE</span></span>
* <span data-ttu-id="8091c-538">事件日志</span><span class="sxs-lookup"><span data-stu-id="8091c-538">EVENTLOG</span></span>
* <span data-ttu-id="8091c-539">文件</span><span class="sxs-lookup"><span data-stu-id="8091c-539">FILE</span></span>

<span data-ttu-id="8091c-540">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="8091c-540">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="8091c-541">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; 调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-541">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="8091c-542">（默认值：aspnetcore debug.log） </span><span class="sxs-lookup"><span data-stu-id="8091c-542">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="8091c-543">`ASPNETCORE_MODULE_DEBUG` &ndash; 调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="8091c-543">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="8091c-544">部署中启用的调试日志记录的时间不要超出排除故障所需的时间  。</span><span class="sxs-lookup"><span data-stu-id="8091c-544">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="8091c-545">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="8091c-545">The size of the log isn't limited.</span></span> <span data-ttu-id="8091c-546">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="8091c-546">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="8091c-547">有关 web.config  文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="8091c-547">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="8091c-548">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="8091c-548">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="8091c-549">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="8091c-549">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="8091c-550">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="8091c-550">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="8091c-551">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="8091c-551">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="8091c-552">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="8091c-552">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="8091c-553">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-553">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="8091c-554">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-554">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="8091c-555">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="8091c-555">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="8091c-556">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-556">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="8091c-557">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="8091c-557">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="8091c-558">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-558">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="8091c-559">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8091c-559">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="8091c-560">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行  。</span><span class="sxs-lookup"><span data-stu-id="8091c-560">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="8091c-561">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误  。</span><span class="sxs-lookup"><span data-stu-id="8091c-561">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="8091c-562">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="8091c-562">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="8091c-563">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="8091c-563">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="8091c-564">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8091c-564">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="8091c-565">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="8091c-565">Run the installer.</span></span>
1. <span data-ttu-id="8091c-566">将已更新的 applicationHost.config  文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="8091c-566">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="8091c-567">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8091c-567">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="8091c-568">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="8091c-568">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="8091c-569">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8091c-569">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="8091c-570">在托管系统上，导航到 %windir%\System32\inetsrv  。</span><span class="sxs-lookup"><span data-stu-id="8091c-570">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="8091c-571">找到 aspnetcore.dll  文件。</span><span class="sxs-lookup"><span data-stu-id="8091c-571">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="8091c-572">右键单击该文件，然后从上下文菜单中选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="8091c-572">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="8091c-573">选择“详细信息”选项卡  。“文件版本”  和“产品版本”  表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="8091c-573">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="8091c-574">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp  中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log  。</span><span class="sxs-lookup"><span data-stu-id="8091c-574">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="8091c-575">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="8091c-575">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="8091c-576">模块</span><span class="sxs-lookup"><span data-stu-id="8091c-576">Module</span></span>

<span data-ttu-id="8091c-577">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8091c-577">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="8091c-578">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-578">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="8091c-579">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-579">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="8091c-580">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-580">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="8091c-581">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-581">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="8091c-582">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8091c-582">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="8091c-583">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-583">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="8091c-584">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-584">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="8091c-585">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-585">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="8091c-586">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-586">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="8091c-587">架构</span><span class="sxs-lookup"><span data-stu-id="8091c-587">Schema</span></span>

<span data-ttu-id="8091c-588">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8091c-588">**IIS**</span></span>

* <span data-ttu-id="8091c-589">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8091c-589">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="8091c-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="8091c-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="8091c-591">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8091c-591">**IIS Express**</span></span>

* <span data-ttu-id="8091c-592">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8091c-592">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="8091c-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="8091c-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="8091c-594">配置</span><span class="sxs-lookup"><span data-stu-id="8091c-594">Configuration</span></span>

<span data-ttu-id="8091c-595">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8091c-595">**IIS**</span></span>

* <span data-ttu-id="8091c-596">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8091c-596">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="8091c-597">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8091c-597">**IIS Express**</span></span>

* <span data-ttu-id="8091c-598">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8091c-598">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="8091c-599"> iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="8091c-599">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="8091c-600">可以通过在 applicationHost.config  文件中搜索 aspnetcore  找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="8091c-600">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="8091c-601">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于将 Web 请求转发到后端 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="8091c-601">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="8091c-602">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="8091c-602">Supported Windows versions:</span></span>

* <span data-ttu-id="8091c-603">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8091c-603">Windows 7 or later</span></span>
* <span data-ttu-id="8091c-604">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8091c-604">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="8091c-605">该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="8091c-605">The module only works with Kestrel.</span></span> <span data-ttu-id="8091c-606">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="8091c-606">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="8091c-607">由于 ASP.NET Core 应用在独立于 IIS 辅助进程的进程中运行，因此该模块还处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="8091c-607">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="8091c-608">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="8091c-608">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="8091c-609">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的 IIS 中在进程内运行的 ASP.NET 4.x 应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="8091c-609">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="8091c-610">下图说明了 IIS、ASP.NET Core 模块和应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="8091c-610">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模块](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="8091c-612">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="8091c-612">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="8091c-613">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="8091c-613">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="8091c-614">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="8091c-614">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="8091c-615">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="8091c-615">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="8091c-616">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-616">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="8091c-617">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="8091c-617">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="8091c-618">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="8091c-618">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="8091c-619">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8091c-619">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="8091c-620">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="8091c-620">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="8091c-621">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="8091c-621">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="8091c-622">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="8091c-622">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="8091c-623">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="8091c-623">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="8091c-624">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="8091c-624">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="8091c-625">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-625">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="8091c-626">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="8091c-626">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="8091c-627">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="8091c-627">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="8091c-628">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8091c-628">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="8091c-629">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="8091c-629">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="8091c-630">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="8091c-630">Configuration with web.config</span></span>

<span data-ttu-id="8091c-631">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="8091c-631">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="8091c-632">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="8091c-632">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="8091c-633">以下 web.config  发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8091c-633">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="8091c-634">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8091c-634">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="8091c-635">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="8091c-635">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="8091c-636">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="8091c-636">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="8091c-637">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="8091c-637">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="8091c-638">特性</span><span class="sxs-lookup"><span data-stu-id="8091c-638">Attribute</span></span> | <span data-ttu-id="8091c-639">说明</span><span class="sxs-lookup"><span data-stu-id="8091c-639">Description</span></span> | <span data-ttu-id="8091c-640">默认</span><span class="sxs-lookup"><span data-stu-id="8091c-640">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="8091c-641">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-641">Optional string attribute.</span></span></p><p><span data-ttu-id="8091c-642">processPath  中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="8091c-642">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="8091c-643">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-643">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8091c-644">如果为 true，将禁止显示“502.5 - 进程失败”  页面，而会优先显示 web.config  中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="8091c-644">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="8091c-645">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-645">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8091c-646">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-646">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="8091c-647">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="8091c-647">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="8091c-648">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-648">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-649">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="8091c-649">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="8091c-650">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="8091c-650">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="8091c-651">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-651">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="8091c-652">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="8091c-652">Default: `1`</span></span><br><span data-ttu-id="8091c-653">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="8091c-653">Min: `1`</span></span><br><span data-ttu-id="8091c-654">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="8091c-654">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="8091c-655">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-655">Required string attribute.</span></span></p><p><span data-ttu-id="8091c-656">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-656">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="8091c-657">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-657">Relative paths are supported.</span></span> <span data-ttu-id="8091c-658">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8091c-658">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="8091c-659">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-659">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-660">指定允许 processPath  中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="8091c-660">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="8091c-661">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-661">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="8091c-662">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8091c-662">Default: `10`</span></span><br><span data-ttu-id="8091c-663">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8091c-663">Min: `0`</span></span><br><span data-ttu-id="8091c-664">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="8091c-664">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="8091c-665">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-665">Optional timespan attribute.</span></span></p><p><span data-ttu-id="8091c-666">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="8091c-666">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="8091c-667">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="8091c-667">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="8091c-668">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="8091c-668">Default: `00:02:00`</span></span><br><span data-ttu-id="8091c-669">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="8091c-669">Min: `00:00:00`</span></span><br><span data-ttu-id="8091c-670">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="8091c-670">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="8091c-671">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-671">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-672">检测到 app_offline.htm  文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8091c-672">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="8091c-673">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8091c-673">Default: `10`</span></span><br><span data-ttu-id="8091c-674">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8091c-674">Min: `0`</span></span><br><span data-ttu-id="8091c-675">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="8091c-675">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="8091c-676">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-676">Optional integer attribute.</span></span></p><p><span data-ttu-id="8091c-677">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8091c-677">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="8091c-678">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-678">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="8091c-679">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute  次。</span><span class="sxs-lookup"><span data-stu-id="8091c-679">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="8091c-680">值 0（零）  不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="8091c-680">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="8091c-681">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="8091c-681">Default: `120`</span></span><br><span data-ttu-id="8091c-682">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8091c-682">Min: `0`</span></span><br><span data-ttu-id="8091c-683">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="8091c-683">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="8091c-684">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-684">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8091c-685">如果为 true，processPath  中指定的 进程的 stdout  和 stderr  将重定向到 stdoutLogFile  中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="8091c-685">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="8091c-686">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-686">Optional string attribute.</span></span></p><p><span data-ttu-id="8091c-687">指定在其中记录 processPath  中指定进程的 stdout  和 stderr  的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-687">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="8091c-688">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8091c-688">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="8091c-689">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-689">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="8091c-690">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="8091c-690">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="8091c-691">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log  ) 添加到 stdoutLogFile  路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="8091c-691">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="8091c-692">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs  文件夹中保存为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8091c-692">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="8091c-693">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="8091c-693">Setting environment variables</span></span>

<span data-ttu-id="8091c-694">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-694">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="8091c-695">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-695">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="8091c-696">本部分中设置的环境变量与使用同一名称设置的系统环境变量冲突。</span><span class="sxs-lookup"><span data-stu-id="8091c-696">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="8091c-697">如果在 web.config  文件以及 Windows 中的系统级别中同时设置了环境变量，则 web.config  文件中的值将被追加到系统环境变量值（例如，`ASPNETCORE_ENVIRONMENT: Development;Development`），这将阻止应用启动。</span><span class="sxs-lookup"><span data-stu-id="8091c-697">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="8091c-698">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="8091c-698">The following example sets two environment variables.</span></span> <span data-ttu-id="8091c-699">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8091c-699">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="8091c-700">开发人员可能会暂时在 web.config  文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="8091c-700">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="8091c-701">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-701">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="8091c-702">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8091c-702">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="8091c-703">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="8091c-703">app_offline.htm</span></span>

<span data-ttu-id="8091c-704">如果在应用的根目录中检测到名为 “app_offline.htm”  的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-704">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="8091c-705">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-705">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="8091c-706">存在 app_offline.htm  文件时，ASP.NET Core 模块会通过发送回 app_offline.htm  文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-706">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="8091c-707">删除 app_offline.htm  文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="8091c-707">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="8091c-708">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="8091c-708">Start-up error page</span></span>

<span data-ttu-id="8091c-709">如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”  状态代码页。</span><span class="sxs-lookup"><span data-stu-id="8091c-709">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="8091c-710">若要禁止显示此页面并还原为默认 IIS 502 状态代码页面，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="8091c-710">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="8091c-711">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="8091c-711">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![“502.5 进程失败”状态代码页面](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="8091c-713">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="8091c-713">Log creation and redirection</span></span>

<span data-ttu-id="8091c-714">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="8091c-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="8091c-715">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="8091c-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="8091c-716">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8091c-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8091c-717">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="8091c-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="8091c-718">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="8091c-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="8091c-719">仅建议使用 stdout 日志来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="8091c-719">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="8091c-720">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="8091c-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="8091c-721">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8091c-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8091c-722">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8091c-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="8091c-723">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="8091c-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="8091c-724">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log  ) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout  ）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="8091c-724">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="8091c-725">如果 `stdoutLogFile` 路径以 stdout  结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8091c-725">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="8091c-726">以下示例 `aspNetCore` 元素为 Azure 应用服务中托管的应用配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="8091c-726">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="8091c-727">本地日志记录可以接受本地路径或网络共享路径。</span><span class="sxs-lookup"><span data-stu-id="8091c-727">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="8091c-728">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="8091c-728">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

<span data-ttu-id="8091c-729">提供给 `<handlerSetting>` 值（上述示例中为 logs  ）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="8091c-729">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="8091c-730">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8091c-730">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8091c-731">有关 web.config  文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="8091c-731">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="8091c-732">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="8091c-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="8091c-733">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="8091c-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="8091c-734">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="8091c-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="8091c-735">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="8091c-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="8091c-736">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="8091c-737">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8091c-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="8091c-738">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="8091c-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="8091c-739">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="8091c-740">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="8091c-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="8091c-741">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="8091c-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="8091c-742">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8091c-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="8091c-743">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行  。</span><span class="sxs-lookup"><span data-stu-id="8091c-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="8091c-744">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误  。</span><span class="sxs-lookup"><span data-stu-id="8091c-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="8091c-745">使用 IIS 共享配置时，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="8091c-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="8091c-746">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8091c-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="8091c-747">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="8091c-747">Run the installer.</span></span>
1. <span data-ttu-id="8091c-748">将已更新的 applicationHost.config  文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="8091c-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="8091c-749">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8091c-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="8091c-750">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="8091c-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="8091c-751">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8091c-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="8091c-752">在托管系统上，导航到 %windir%\System32\inetsrv  。</span><span class="sxs-lookup"><span data-stu-id="8091c-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="8091c-753">找到 aspnetcore.dll  文件。</span><span class="sxs-lookup"><span data-stu-id="8091c-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="8091c-754">右键单击该文件，然后从上下文菜单中选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="8091c-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="8091c-755">选择“详细信息”选项卡  。“文件版本”  和“产品版本”  表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="8091c-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="8091c-756">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp  中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log  。</span><span class="sxs-lookup"><span data-stu-id="8091c-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="8091c-757">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="8091c-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="8091c-758">模块</span><span class="sxs-lookup"><span data-stu-id="8091c-758">Module</span></span>

<span data-ttu-id="8091c-759">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8091c-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="8091c-760">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="8091c-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="8091c-762">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8091c-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="8091c-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="8091c-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8091c-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="8091c-765">架构</span><span class="sxs-lookup"><span data-stu-id="8091c-765">Schema</span></span>

<span data-ttu-id="8091c-766">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8091c-766">**IIS**</span></span>

* <span data-ttu-id="8091c-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8091c-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="8091c-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8091c-768">**IIS Express**</span></span>

* <span data-ttu-id="8091c-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8091c-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="8091c-770">配置</span><span class="sxs-lookup"><span data-stu-id="8091c-770">Configuration</span></span>

<span data-ttu-id="8091c-771">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8091c-771">**IIS**</span></span>

* <span data-ttu-id="8091c-772">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8091c-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="8091c-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8091c-773">**IIS Express**</span></span>

* <span data-ttu-id="8091c-774">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8091c-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="8091c-775"> iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="8091c-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="8091c-776">可以通过在 applicationHost.config  文件中搜索 aspnetcore  找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="8091c-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8091c-777">其他资源</span><span class="sxs-lookup"><span data-stu-id="8091c-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="8091c-778">ASP.NET Core 模块 GitHub 存储库（引用源）</span><span class="sxs-lookup"><span data-stu-id="8091c-778">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
