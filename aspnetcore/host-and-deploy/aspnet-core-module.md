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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: c9cc340c9a3d6ff1b3783686bd779dfe04b14275
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775266"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="8f17a-103">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8f17a-103">ASP.NET Core Module</span></span>

<span data-ttu-id="8f17a-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti) 和 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="8f17a-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8f17a-105">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="8f17a-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="8f17a-106">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="8f17a-107">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="8f17a-108">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="8f17a-108">Supported Windows versions:</span></span>

* <span data-ttu-id="8f17a-109">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8f17a-109">Windows 7 or later</span></span>
* <span data-ttu-id="8f17a-110">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8f17a-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="8f17a-111">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="8f17a-112">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="8f17a-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="8f17a-113">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="8f17a-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="8f17a-114">托管模型</span><span class="sxs-lookup"><span data-stu-id="8f17a-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="8f17a-115">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="8f17a-115">In-process hosting model</span></span>

<span data-ttu-id="8f17a-116">ASP.NET Core 应用默认为进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="8f17a-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="8f17a-117">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="8f17a-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="8f17a-118">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="8f17a-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="8f17a-119">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8f17a-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="8f17a-120">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="8f17a-121">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="8f17a-122">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="8f17a-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="8f17a-123">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8f17a-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="8f17a-124">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="8f17a-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="8f17a-125">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="8f17a-125">Use one app pool per app.</span></span>

* <span data-ttu-id="8f17a-126">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="8f17a-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="8f17a-127">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="8f17a-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="8f17a-128">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="8f17a-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="8f17a-129">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="8f17a-129">Client disconnects are detected.</span></span> <span data-ttu-id="8f17a-130">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="8f17a-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="8f17a-131">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv   ）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="8f17a-132">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="8f17a-133">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="8f17a-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="8f17a-134">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="8f17a-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="8f17a-135">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="8f17a-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="8f17a-136">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="8f17a-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="8f17a-137">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="8f17a-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="8f17a-138">不支持 [Web 包（单文件）部署](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="8f17a-139">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="8f17a-139">Out-of-process hosting model</span></span>

<span data-ttu-id="8f17a-140">若要配置进程外托管应用，请在项目文件 ( *.csproj*) 中将 `<AspNetCoreHostingModel>` 属性的值设置为 `OutOfProcess`：</span><span class="sxs-lookup"><span data-stu-id="8f17a-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="8f17a-141">进程内托管设为 `InProcess`，这是默认值。</span><span class="sxs-lookup"><span data-stu-id="8f17a-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="8f17a-142">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="8f17a-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="8f17a-143">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="8f17a-144">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8f17a-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="8f17a-145">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="8f17a-146">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="8f17a-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="8f17a-147">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="8f17a-147">Hosting model changes</span></span>

<span data-ttu-id="8f17a-148">如果 `hostingModel` 设置在 web.config  文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="8f17a-149">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8f17a-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="8f17a-150">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="8f17a-151">进程名</span><span class="sxs-lookup"><span data-stu-id="8f17a-151">Process name</span></span>

<span data-ttu-id="8f17a-152">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="8f17a-153">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="8f17a-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="8f17a-154">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="8f17a-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="8f17a-155">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="8f17a-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="8f17a-156">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="8f17a-157">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="8f17a-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="8f17a-158">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="8f17a-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="8f17a-159">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8f17a-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="8f17a-160">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="8f17a-161">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="8f17a-161">Configuration with web.config</span></span>

<span data-ttu-id="8f17a-162">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="8f17a-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="8f17a-163">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="8f17a-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="8f17a-164">以下 web.config  发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8f17a-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="8f17a-165">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="8f17a-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="8f17a-166">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="8f17a-167">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="8f17a-168">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="8f17a-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="8f17a-169">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="8f17a-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="8f17a-170">特性</span><span class="sxs-lookup"><span data-stu-id="8f17a-170">Attribute</span></span> | <span data-ttu-id="8f17a-171">描述</span><span class="sxs-lookup"><span data-stu-id="8f17a-171">Description</span></span> | <span data-ttu-id="8f17a-172">默认</span><span class="sxs-lookup"><span data-stu-id="8f17a-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="8f17a-173">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-173">Optional string attribute.</span></span></p><p><span data-ttu-id="8f17a-174">processPath  中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="8f17a-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="8f17a-175">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8f17a-176">如果为 true，将禁止显示“502.5 - 进程失败”  页面，而会优先显示 web.config  中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="8f17a-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="8f17a-177">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8f17a-178">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="8f17a-179">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="8f17a-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="8f17a-180">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-180">Optional string attribute.</span></span></p><p><span data-ttu-id="8f17a-181">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="8f17a-182">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-183">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="8f17a-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="8f17a-184">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="8f17a-185">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="8f17a-186">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="8f17a-187">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="8f17a-187">Default: `1`</span></span><br><span data-ttu-id="8f17a-188">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="8f17a-188">Min: `1`</span></span><br><span data-ttu-id="8f17a-189">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="8f17a-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="8f17a-190">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-190">Required string attribute.</span></span></p><p><span data-ttu-id="8f17a-191">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="8f17a-192">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-192">Relative paths are supported.</span></span> <span data-ttu-id="8f17a-193">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8f17a-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="8f17a-194">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-195">指定允许 processPath  中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="8f17a-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="8f17a-196">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="8f17a-197">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8f17a-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="8f17a-198">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8f17a-198">Default: `10`</span></span><br><span data-ttu-id="8f17a-199">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8f17a-199">Min: `0`</span></span><br><span data-ttu-id="8f17a-200">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="8f17a-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="8f17a-201">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="8f17a-202">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="8f17a-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="8f17a-203">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="8f17a-204">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8f17a-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="8f17a-205">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="8f17a-206">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="8f17a-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="8f17a-207">在分钟或秒钟值中使用“60”  会导致“500 - 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="8f17a-208">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="8f17a-208">Default: `00:02:00`</span></span><br><span data-ttu-id="8f17a-209">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="8f17a-209">Min: `00:00:00`</span></span><br><span data-ttu-id="8f17a-210">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="8f17a-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="8f17a-211">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-212">检测到 app_offline.htm  文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="8f17a-213">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8f17a-213">Default: `10`</span></span><br><span data-ttu-id="8f17a-214">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8f17a-214">Min: `0`</span></span><br><span data-ttu-id="8f17a-215">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="8f17a-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="8f17a-216">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-217">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="8f17a-218">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="8f17a-219">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute  次。</span><span class="sxs-lookup"><span data-stu-id="8f17a-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="8f17a-220">值 0（零）  不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="8f17a-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="8f17a-221">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="8f17a-221">Default: `120`</span></span><br><span data-ttu-id="8f17a-222">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8f17a-222">Min: `0`</span></span><br><span data-ttu-id="8f17a-223">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="8f17a-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="8f17a-224">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8f17a-225">如果为 true，processPath  中指定的 进程的 stdout  和 stderr  将重定向到 stdoutLogFile  中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="8f17a-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="8f17a-226">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-226">Optional string attribute.</span></span></p><p><span data-ttu-id="8f17a-227">指定在其中记录 processPath  中指定进程的 stdout  和 stderr  的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="8f17a-228">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8f17a-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="8f17a-229">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="8f17a-230">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="8f17a-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="8f17a-231">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log  ) 添加到 stdoutLogFile  路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="8f17a-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="8f17a-232">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs  文件夹中保存为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="8f17a-233">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="8f17a-233">Set environment variables</span></span>

<span data-ttu-id="8f17a-234">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="8f17a-235">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="8f17a-236">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="8f17a-237">以下示例在 web.config  中设置两个环境变量。`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="8f17a-238">开发人员可能会暂时在 web.config  文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="8f17a-239">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="8f17a-240">直接在 web.config  中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="8f17a-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="8f17a-241">此方法在发布项目时设置 web.config  中的环境：</span><span class="sxs-lookup"><span data-stu-id="8f17a-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="8f17a-242">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="8f17a-243">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="8f17a-243">app_offline.htm</span></span>

<span data-ttu-id="8f17a-244">如果在应用的根目录中检测到名为 “app_offline.htm”  的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="8f17a-245">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="8f17a-246">存在 app_offline.htm  文件时，ASP.NET Core 模块会通过发送回 app_offline.htm  文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="8f17a-247">删除 app_offline.htm  文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="8f17a-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="8f17a-248">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="8f17a-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="8f17a-249">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="8f17a-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="8f17a-250">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="8f17a-250">Start-up error page</span></span>

<span data-ttu-id="8f17a-251">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="8f17a-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="8f17a-252">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="8f17a-253">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="8f17a-254">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”  状态代码页。</span><span class="sxs-lookup"><span data-stu-id="8f17a-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="8f17a-255">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="8f17a-256">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="8f17a-257">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="8f17a-257">Log creation and redirection</span></span>

<span data-ttu-id="8f17a-258">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="8f17a-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="8f17a-259">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="8f17a-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="8f17a-260">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8f17a-261">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="8f17a-262">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="8f17a-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="8f17a-263">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="8f17a-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="8f17a-264">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="8f17a-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="8f17a-265">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8f17a-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8f17a-266">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="8f17a-267">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="8f17a-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="8f17a-268">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log  ) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout  ）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="8f17a-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="8f17a-269">如果 `stdoutLogFile` 路径以 stdout  结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="8f17a-270">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="8f17a-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="8f17a-271">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="8f17a-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="8f17a-272">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="8f17a-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="8f17a-273">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="8f17a-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="8f17a-274">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="8f17a-275">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="8f17a-276">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="8f17a-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="8f17a-277">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="8f17a-278">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="8f17a-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="8f17a-279">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="8f17a-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="8f17a-280">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素  。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="8f17a-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="8f17a-281">创建日志文件时，路径中的任何文件夹（上述示例中为 logs  ）由模块创建。</span><span class="sxs-lookup"><span data-stu-id="8f17a-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="8f17a-282">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8f17a-283">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="8f17a-284">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="8f17a-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="8f17a-285">错误</span><span class="sxs-lookup"><span data-stu-id="8f17a-285">ERROR</span></span>
* <span data-ttu-id="8f17a-286">警告</span><span class="sxs-lookup"><span data-stu-id="8f17a-286">WARNING</span></span>
* <span data-ttu-id="8f17a-287">信息</span><span class="sxs-lookup"><span data-stu-id="8f17a-287">INFO</span></span>
* <span data-ttu-id="8f17a-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="8f17a-288">TRACE</span></span>

<span data-ttu-id="8f17a-289">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="8f17a-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="8f17a-290">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="8f17a-290">CONSOLE</span></span>
* <span data-ttu-id="8f17a-291">事件日志</span><span class="sxs-lookup"><span data-stu-id="8f17a-291">EVENTLOG</span></span>
* <span data-ttu-id="8f17a-292">文件</span><span class="sxs-lookup"><span data-stu-id="8f17a-292">FILE</span></span>

<span data-ttu-id="8f17a-293">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="8f17a-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="8f17a-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; 调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="8f17a-295">（默认值：aspnetcore debug.log） </span><span class="sxs-lookup"><span data-stu-id="8f17a-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="8f17a-296">`ASPNETCORE_MODULE_DEBUG` &ndash; 调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="8f17a-297">部署中启用的调试日志记录的时间不要超出排除故障所需的时间  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="8f17a-298">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="8f17a-298">The size of the log isn't limited.</span></span> <span data-ttu-id="8f17a-299">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="8f17a-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="8f17a-300">有关 web.config  文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="8f17a-301">修改堆栈大小</span><span class="sxs-lookup"><span data-stu-id="8f17a-301">Modify the stack size</span></span>

<span data-ttu-id="8f17a-302">*仅适用于使用进程内托管模型的情况。*</span><span class="sxs-lookup"><span data-stu-id="8f17a-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="8f17a-303">使用 web.config 中的 `stackSize` 设置（以字节为单位）配置托管堆栈大小  。默认大小为 `1048576` 个字节 (1 MB)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="8f17a-304">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="8f17a-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="8f17a-305">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="8f17a-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="8f17a-306">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="8f17a-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="8f17a-307">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="8f17a-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="8f17a-308">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="8f17a-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="8f17a-309">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="8f17a-310">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="8f17a-311">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="8f17a-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="8f17a-312">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="8f17a-313">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="8f17a-314">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="8f17a-315">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8f17a-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="8f17a-316">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="8f17a-317">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="8f17a-318">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="8f17a-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="8f17a-319">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="8f17a-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="8f17a-320">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="8f17a-321">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="8f17a-321">Run the installer.</span></span>
1. <span data-ttu-id="8f17a-322">将已更新的 applicationHost.config  文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="8f17a-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="8f17a-323">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="8f17a-324">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="8f17a-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="8f17a-325">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8f17a-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="8f17a-326">在托管系统上，导航到 %windir%\System32\inetsrv  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="8f17a-327">找到 aspnetcore.dll  文件。</span><span class="sxs-lookup"><span data-stu-id="8f17a-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="8f17a-328">右键单击该文件，然后从上下文菜单中选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="8f17a-329">选择“详细信息”选项卡  。“文件版本”  和“产品版本”  表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="8f17a-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="8f17a-330">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp  中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="8f17a-331">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="8f17a-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="8f17a-332">模块</span><span class="sxs-lookup"><span data-stu-id="8f17a-332">Module</span></span>

<span data-ttu-id="8f17a-333">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8f17a-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="8f17a-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="8f17a-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="8f17a-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="8f17a-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="8f17a-338">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8f17a-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="8f17a-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="8f17a-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="8f17a-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="8f17a-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="8f17a-343">架构</span><span class="sxs-lookup"><span data-stu-id="8f17a-343">Schema</span></span>

<span data-ttu-id="8f17a-344">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8f17a-344">**IIS**</span></span>

* <span data-ttu-id="8f17a-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8f17a-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="8f17a-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="8f17a-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="8f17a-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8f17a-347">**IIS Express**</span></span>

* <span data-ttu-id="8f17a-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8f17a-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="8f17a-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="8f17a-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="8f17a-350">Configuration</span><span class="sxs-lookup"><span data-stu-id="8f17a-350">Configuration</span></span>

<span data-ttu-id="8f17a-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8f17a-351">**IIS**</span></span>

* <span data-ttu-id="8f17a-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8f17a-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="8f17a-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8f17a-353">**IIS Express**</span></span>

* <span data-ttu-id="8f17a-354">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8f17a-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="8f17a-355"> iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="8f17a-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="8f17a-356">可以通过在 applicationHost.config  文件中搜索 aspnetcore  找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="8f17a-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="8f17a-357">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="8f17a-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="8f17a-358">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="8f17a-359">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="8f17a-360">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="8f17a-360">Supported Windows versions:</span></span>

* <span data-ttu-id="8f17a-361">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8f17a-361">Windows 7 or later</span></span>
* <span data-ttu-id="8f17a-362">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8f17a-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="8f17a-363">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="8f17a-364">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="8f17a-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="8f17a-365">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="8f17a-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="8f17a-366">托管模型</span><span class="sxs-lookup"><span data-stu-id="8f17a-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="8f17a-367">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="8f17a-367">In-process hosting model</span></span>

<span data-ttu-id="8f17a-368">若要配置用于进程内托管的应用，请将 `<AspNetCoreHostingModel>` 属性添加到值为 `InProcess`（进程外托管使用 `OutOfProcess` 进行设置）的应用项目文件：</span><span class="sxs-lookup"><span data-stu-id="8f17a-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="8f17a-369">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="8f17a-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="8f17a-370">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="8f17a-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="8f17a-371">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="8f17a-372">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="8f17a-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="8f17a-373">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="8f17a-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="8f17a-374">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8f17a-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="8f17a-375">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="8f17a-376">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="8f17a-377">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="8f17a-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="8f17a-378">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8f17a-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="8f17a-379">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="8f17a-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="8f17a-380">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="8f17a-380">Use one app pool per app.</span></span>

* <span data-ttu-id="8f17a-381">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="8f17a-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="8f17a-382">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="8f17a-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="8f17a-383">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="8f17a-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="8f17a-384">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="8f17a-384">Client disconnects are detected.</span></span> <span data-ttu-id="8f17a-385">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="8f17a-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="8f17a-386">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv   ）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="8f17a-387">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="8f17a-388">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="8f17a-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="8f17a-389">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="8f17a-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="8f17a-390">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="8f17a-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="8f17a-391">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="8f17a-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="8f17a-392">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="8f17a-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="8f17a-393">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="8f17a-393">Out-of-process hosting model</span></span>

<span data-ttu-id="8f17a-394">若要配置进程外托管应用，请在项目文件中使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="8f17a-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="8f17a-395">不指定 `<AspNetCoreHostingModel>` 属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="8f17a-396">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="8f17a-397">将 `<AspNetCoreHostingModel>` 属性的值设为 `OutOfProcess`（进程内托管设为 `InProcess`）：</span><span class="sxs-lookup"><span data-stu-id="8f17a-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="8f17a-398">值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="8f17a-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="8f17a-399">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="8f17a-400">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8f17a-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="8f17a-401">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="8f17a-402">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="8f17a-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="8f17a-403">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="8f17a-403">Hosting model changes</span></span>

<span data-ttu-id="8f17a-404">如果 `hostingModel` 设置在 web.config  文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="8f17a-405">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8f17a-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="8f17a-406">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="8f17a-407">进程名</span><span class="sxs-lookup"><span data-stu-id="8f17a-407">Process name</span></span>

<span data-ttu-id="8f17a-408">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="8f17a-409">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="8f17a-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="8f17a-410">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="8f17a-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="8f17a-411">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="8f17a-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="8f17a-412">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="8f17a-413">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="8f17a-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="8f17a-414">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="8f17a-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="8f17a-415">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8f17a-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="8f17a-416">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="8f17a-417">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="8f17a-417">Configuration with web.config</span></span>

<span data-ttu-id="8f17a-418">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="8f17a-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="8f17a-419">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="8f17a-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="8f17a-420">以下 web.config  发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8f17a-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="8f17a-421">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="8f17a-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="8f17a-422">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="8f17a-423">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="8f17a-424">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="8f17a-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="8f17a-425">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="8f17a-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="8f17a-426">特性</span><span class="sxs-lookup"><span data-stu-id="8f17a-426">Attribute</span></span> | <span data-ttu-id="8f17a-427">描述</span><span class="sxs-lookup"><span data-stu-id="8f17a-427">Description</span></span> | <span data-ttu-id="8f17a-428">默认</span><span class="sxs-lookup"><span data-stu-id="8f17a-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="8f17a-429">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-429">Optional string attribute.</span></span></p><p><span data-ttu-id="8f17a-430">processPath  中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="8f17a-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="8f17a-431">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8f17a-432">如果为 true，将禁止显示“502.5 - 进程失败”  页面，而会优先显示 web.config  中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="8f17a-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="8f17a-433">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8f17a-434">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="8f17a-435">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="8f17a-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="8f17a-436">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-436">Optional string attribute.</span></span></p><p><span data-ttu-id="8f17a-437">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="8f17a-438">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-439">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="8f17a-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="8f17a-440">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="8f17a-441">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="8f17a-442">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="8f17a-443">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="8f17a-443">Default: `1`</span></span><br><span data-ttu-id="8f17a-444">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="8f17a-444">Min: `1`</span></span><br><span data-ttu-id="8f17a-445">最大值：`100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="8f17a-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="8f17a-446">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-446">Required string attribute.</span></span></p><p><span data-ttu-id="8f17a-447">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="8f17a-448">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-448">Relative paths are supported.</span></span> <span data-ttu-id="8f17a-449">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8f17a-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="8f17a-450">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-451">指定允许 processPath  中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="8f17a-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="8f17a-452">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="8f17a-453">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8f17a-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="8f17a-454">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8f17a-454">Default: `10`</span></span><br><span data-ttu-id="8f17a-455">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8f17a-455">Min: `0`</span></span><br><span data-ttu-id="8f17a-456">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="8f17a-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="8f17a-457">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="8f17a-458">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="8f17a-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="8f17a-459">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="8f17a-460">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="8f17a-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="8f17a-461">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="8f17a-462">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="8f17a-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="8f17a-463">在分钟或秒钟值中使用“60”  会导致“500 - 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="8f17a-464">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="8f17a-464">Default: `00:02:00`</span></span><br><span data-ttu-id="8f17a-465">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="8f17a-465">Min: `00:00:00`</span></span><br><span data-ttu-id="8f17a-466">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="8f17a-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="8f17a-467">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-468">检测到 app_offline.htm  文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="8f17a-469">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8f17a-469">Default: `10`</span></span><br><span data-ttu-id="8f17a-470">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8f17a-470">Min: `0`</span></span><br><span data-ttu-id="8f17a-471">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="8f17a-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="8f17a-472">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-473">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="8f17a-474">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="8f17a-475">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute  次。</span><span class="sxs-lookup"><span data-stu-id="8f17a-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="8f17a-476">值 0（零）  不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="8f17a-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="8f17a-477">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="8f17a-477">Default: `120`</span></span><br><span data-ttu-id="8f17a-478">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8f17a-478">Min: `0`</span></span><br><span data-ttu-id="8f17a-479">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="8f17a-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="8f17a-480">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8f17a-481">如果为 true，processPath  中指定的 进程的 stdout  和 stderr  将重定向到 stdoutLogFile  中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="8f17a-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="8f17a-482">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-482">Optional string attribute.</span></span></p><p><span data-ttu-id="8f17a-483">指定在其中记录 processPath  中指定进程的 stdout  和 stderr  的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="8f17a-484">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8f17a-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="8f17a-485">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="8f17a-486">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="8f17a-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="8f17a-487">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log  ) 添加到 stdoutLogFile  路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="8f17a-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="8f17a-488">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs  文件夹中保存为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="8f17a-489">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="8f17a-489">Setting environment variables</span></span>

<span data-ttu-id="8f17a-490">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="8f17a-491">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="8f17a-492">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="8f17a-493">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-493">The following example sets two environment variables.</span></span> <span data-ttu-id="8f17a-494">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="8f17a-495">开发人员可能会暂时在 web.config  文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="8f17a-496">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="8f17a-497">直接在 web.config  中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="8f17a-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="8f17a-498">此方法在发布项目时设置 web.config  中的环境：</span><span class="sxs-lookup"><span data-stu-id="8f17a-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="8f17a-499">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="8f17a-500">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="8f17a-500">app_offline.htm</span></span>

<span data-ttu-id="8f17a-501">如果在应用的根目录中检测到名为 “app_offline.htm”  的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="8f17a-502">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="8f17a-503">存在 app_offline.htm  文件时，ASP.NET Core 模块会通过发送回 app_offline.htm  文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="8f17a-504">删除 app_offline.htm  文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="8f17a-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="8f17a-505">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="8f17a-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="8f17a-506">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="8f17a-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="8f17a-507">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="8f17a-507">Start-up error page</span></span>

<span data-ttu-id="8f17a-508">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="8f17a-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="8f17a-509">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="8f17a-510">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="8f17a-511">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”  状态代码页。</span><span class="sxs-lookup"><span data-stu-id="8f17a-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="8f17a-512">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="8f17a-513">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="8f17a-514">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="8f17a-514">Log creation and redirection</span></span>

<span data-ttu-id="8f17a-515">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="8f17a-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="8f17a-516">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="8f17a-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="8f17a-517">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8f17a-518">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="8f17a-519">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="8f17a-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="8f17a-520">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="8f17a-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="8f17a-521">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="8f17a-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="8f17a-522">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8f17a-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8f17a-523">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="8f17a-524">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="8f17a-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="8f17a-525">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log  ) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout  ）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="8f17a-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="8f17a-526">如果 `stdoutLogFile` 路径以 stdout  结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="8f17a-527">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="8f17a-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="8f17a-528">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="8f17a-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="8f17a-529">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="8f17a-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="8f17a-530">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="8f17a-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="8f17a-531">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="8f17a-532">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="8f17a-533">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="8f17a-534">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="8f17a-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="8f17a-535">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="8f17a-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="8f17a-536">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素  。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="8f17a-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="8f17a-537">提供给 `<handlerSetting>` 值（上述示例中为 logs  ）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="8f17a-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="8f17a-538">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8f17a-539">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="8f17a-540">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="8f17a-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="8f17a-541">错误</span><span class="sxs-lookup"><span data-stu-id="8f17a-541">ERROR</span></span>
* <span data-ttu-id="8f17a-542">警告</span><span class="sxs-lookup"><span data-stu-id="8f17a-542">WARNING</span></span>
* <span data-ttu-id="8f17a-543">信息</span><span class="sxs-lookup"><span data-stu-id="8f17a-543">INFO</span></span>
* <span data-ttu-id="8f17a-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="8f17a-544">TRACE</span></span>

<span data-ttu-id="8f17a-545">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="8f17a-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="8f17a-546">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="8f17a-546">CONSOLE</span></span>
* <span data-ttu-id="8f17a-547">事件日志</span><span class="sxs-lookup"><span data-stu-id="8f17a-547">EVENTLOG</span></span>
* <span data-ttu-id="8f17a-548">文件</span><span class="sxs-lookup"><span data-stu-id="8f17a-548">FILE</span></span>

<span data-ttu-id="8f17a-549">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="8f17a-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="8f17a-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; 调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="8f17a-551">（默认值：aspnetcore debug.log） </span><span class="sxs-lookup"><span data-stu-id="8f17a-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="8f17a-552">`ASPNETCORE_MODULE_DEBUG` &ndash; 调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="8f17a-553">部署中启用的调试日志记录的时间不要超出排除故障所需的时间  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="8f17a-554">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="8f17a-554">The size of the log isn't limited.</span></span> <span data-ttu-id="8f17a-555">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="8f17a-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="8f17a-556">有关 web.config  文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="8f17a-557">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="8f17a-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="8f17a-558">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="8f17a-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="8f17a-559">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="8f17a-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="8f17a-560">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="8f17a-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="8f17a-561">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="8f17a-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="8f17a-562">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="8f17a-563">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="8f17a-564">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="8f17a-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="8f17a-565">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="8f17a-566">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="8f17a-567">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="8f17a-568">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8f17a-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="8f17a-569">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="8f17a-570">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="8f17a-571">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="8f17a-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="8f17a-572">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="8f17a-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="8f17a-573">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="8f17a-574">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="8f17a-574">Run the installer.</span></span>
1. <span data-ttu-id="8f17a-575">将已更新的 applicationHost.config  文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="8f17a-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="8f17a-576">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="8f17a-577">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="8f17a-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="8f17a-578">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8f17a-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="8f17a-579">在托管系统上，导航到 %windir%\System32\inetsrv  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="8f17a-580">找到 aspnetcore.dll  文件。</span><span class="sxs-lookup"><span data-stu-id="8f17a-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="8f17a-581">右键单击该文件，然后从上下文菜单中选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="8f17a-582">选择“详细信息”选项卡  。“文件版本”  和“产品版本”  表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="8f17a-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="8f17a-583">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp  中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="8f17a-584">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="8f17a-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="8f17a-585">模块</span><span class="sxs-lookup"><span data-stu-id="8f17a-585">Module</span></span>

<span data-ttu-id="8f17a-586">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8f17a-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="8f17a-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="8f17a-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="8f17a-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="8f17a-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="8f17a-591">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8f17a-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="8f17a-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="8f17a-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="8f17a-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="8f17a-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="8f17a-596">架构</span><span class="sxs-lookup"><span data-stu-id="8f17a-596">Schema</span></span>

<span data-ttu-id="8f17a-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8f17a-597">**IIS**</span></span>

* <span data-ttu-id="8f17a-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8f17a-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="8f17a-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="8f17a-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="8f17a-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8f17a-600">**IIS Express**</span></span>

* <span data-ttu-id="8f17a-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8f17a-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="8f17a-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="8f17a-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="8f17a-603">Configuration</span><span class="sxs-lookup"><span data-stu-id="8f17a-603">Configuration</span></span>

<span data-ttu-id="8f17a-604">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8f17a-604">**IIS**</span></span>

* <span data-ttu-id="8f17a-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8f17a-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="8f17a-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8f17a-606">**IIS Express**</span></span>

* <span data-ttu-id="8f17a-607">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8f17a-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="8f17a-608"> iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="8f17a-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="8f17a-609">可以通过在 applicationHost.config  文件中搜索 aspnetcore  找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="8f17a-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="8f17a-610">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于将 Web 请求转发到后端 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="8f17a-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="8f17a-611">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="8f17a-611">Supported Windows versions:</span></span>

* <span data-ttu-id="8f17a-612">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8f17a-612">Windows 7 or later</span></span>
* <span data-ttu-id="8f17a-613">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="8f17a-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="8f17a-614">该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="8f17a-614">The module only works with Kestrel.</span></span> <span data-ttu-id="8f17a-615">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="8f17a-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="8f17a-616">由于 ASP.NET Core 应用在独立于 IIS 辅助进程的进程中运行，因此该模块还处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="8f17a-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="8f17a-617">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="8f17a-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="8f17a-618">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的 IIS 中在进程内运行的 ASP.NET 4.x 应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="8f17a-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="8f17a-619">下图说明了 IIS、ASP.NET Core 模块和应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="8f17a-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模块](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="8f17a-621">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="8f17a-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="8f17a-622">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="8f17a-623">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="8f17a-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="8f17a-624">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="8f17a-625">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="8f17a-626">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="8f17a-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="8f17a-627">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="8f17a-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="8f17a-628">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8f17a-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="8f17a-629">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="8f17a-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="8f17a-630">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="8f17a-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="8f17a-631">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="8f17a-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="8f17a-632">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="8f17a-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="8f17a-633">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="8f17a-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="8f17a-634">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="8f17a-635">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="8f17a-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="8f17a-636">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="8f17a-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="8f17a-637">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8f17a-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="8f17a-638">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="8f17a-639">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="8f17a-639">Configuration with web.config</span></span>

<span data-ttu-id="8f17a-640">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="8f17a-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="8f17a-641">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="8f17a-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="8f17a-642">以下 web.config  发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="8f17a-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="8f17a-643">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="8f17a-644">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="8f17a-645">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="8f17a-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="8f17a-646">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="8f17a-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="8f17a-647">特性</span><span class="sxs-lookup"><span data-stu-id="8f17a-647">Attribute</span></span> | <span data-ttu-id="8f17a-648">描述</span><span class="sxs-lookup"><span data-stu-id="8f17a-648">Description</span></span> | <span data-ttu-id="8f17a-649">默认</span><span class="sxs-lookup"><span data-stu-id="8f17a-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="8f17a-650">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-650">Optional string attribute.</span></span></p><p><span data-ttu-id="8f17a-651">processPath  中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="8f17a-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="8f17a-652">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8f17a-653">如果为 true，将禁止显示“502.5 - 进程失败”  页面，而会优先显示 web.config  中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="8f17a-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="8f17a-654">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8f17a-655">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="8f17a-656">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="8f17a-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="8f17a-657">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-658">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="8f17a-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="8f17a-659">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="8f17a-660">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="8f17a-661">默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="8f17a-661">Default: `1`</span></span><br><span data-ttu-id="8f17a-662">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="8f17a-662">Min: `1`</span></span><br><span data-ttu-id="8f17a-663">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="8f17a-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="8f17a-664">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-664">Required string attribute.</span></span></p><p><span data-ttu-id="8f17a-665">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="8f17a-666">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-666">Relative paths are supported.</span></span> <span data-ttu-id="8f17a-667">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8f17a-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="8f17a-668">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-669">指定允许 processPath  中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="8f17a-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="8f17a-670">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="8f17a-671">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8f17a-671">Default: `10`</span></span><br><span data-ttu-id="8f17a-672">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8f17a-672">Min: `0`</span></span><br><span data-ttu-id="8f17a-673">最大值：`100`</span><span class="sxs-lookup"><span data-stu-id="8f17a-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="8f17a-674">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="8f17a-675">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="8f17a-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="8f17a-676">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="8f17a-677">默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="8f17a-677">Default: `00:02:00`</span></span><br><span data-ttu-id="8f17a-678">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="8f17a-678">Min: `00:00:00`</span></span><br><span data-ttu-id="8f17a-679">最大值：`360:00:00`</span><span class="sxs-lookup"><span data-stu-id="8f17a-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="8f17a-680">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-681">检测到 app_offline.htm  文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="8f17a-682">默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="8f17a-682">Default: `10`</span></span><br><span data-ttu-id="8f17a-683">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8f17a-683">Min: `0`</span></span><br><span data-ttu-id="8f17a-684">最大值：`600`</span><span class="sxs-lookup"><span data-stu-id="8f17a-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="8f17a-685">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="8f17a-686">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="8f17a-687">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="8f17a-688">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute  次。</span><span class="sxs-lookup"><span data-stu-id="8f17a-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="8f17a-689">值 0（零）  不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="8f17a-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="8f17a-690">默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="8f17a-690">Default: `120`</span></span><br><span data-ttu-id="8f17a-691">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="8f17a-691">Min: `0`</span></span><br><span data-ttu-id="8f17a-692">最大值：`3600`</span><span class="sxs-lookup"><span data-stu-id="8f17a-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="8f17a-693">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="8f17a-694">如果为 true，processPath  中指定的 进程的 stdout  和 stderr  将重定向到 stdoutLogFile  中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="8f17a-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="8f17a-695">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-695">Optional string attribute.</span></span></p><p><span data-ttu-id="8f17a-696">指定在其中记录 processPath  中指定进程的 stdout  和 stderr  的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="8f17a-697">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="8f17a-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="8f17a-698">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="8f17a-699">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="8f17a-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="8f17a-700">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log  ) 添加到 stdoutLogFile  路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="8f17a-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="8f17a-701">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs  文件夹中保存为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="8f17a-702">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="8f17a-702">Setting environment variables</span></span>

<span data-ttu-id="8f17a-703">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="8f17a-704">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="8f17a-705">本部分中设置的环境变量与使用同一名称设置的系统环境变量冲突。</span><span class="sxs-lookup"><span data-stu-id="8f17a-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="8f17a-706">如果在 web.config  文件以及 Windows 中的系统级别中同时设置了环境变量，则 web.config  文件中的值将被追加到系统环境变量值（例如，`ASPNETCORE_ENVIRONMENT: Development;Development`），这将阻止应用启动。</span><span class="sxs-lookup"><span data-stu-id="8f17a-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="8f17a-707">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-707">The following example sets two environment variables.</span></span> <span data-ttu-id="8f17a-708">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="8f17a-709">开发人员可能会暂时在 web.config  文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="8f17a-710">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8f17a-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="8f17a-711">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="8f17a-712">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="8f17a-712">app_offline.htm</span></span>

<span data-ttu-id="8f17a-713">如果在应用的根目录中检测到名为 “app_offline.htm”  的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="8f17a-714">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="8f17a-715">存在 app_offline.htm  文件时，ASP.NET Core 模块会通过发送回 app_offline.htm  文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="8f17a-716">删除 app_offline.htm  文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="8f17a-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="8f17a-717">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="8f17a-717">Start-up error page</span></span>

<span data-ttu-id="8f17a-718">如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”  状态代码页。</span><span class="sxs-lookup"><span data-stu-id="8f17a-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="8f17a-719">若要禁止显示此页面并还原为默认 IIS 502 状态代码页面，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="8f17a-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="8f17a-720">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![“502.5 进程失败”状态代码页面](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="8f17a-722">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="8f17a-722">Log creation and redirection</span></span>

<span data-ttu-id="8f17a-723">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="8f17a-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="8f17a-724">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="8f17a-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="8f17a-725">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="8f17a-726">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="8f17a-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="8f17a-727">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="8f17a-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="8f17a-728">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="8f17a-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="8f17a-729">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="8f17a-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="8f17a-730">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="8f17a-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="8f17a-731">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="8f17a-732">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="8f17a-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="8f17a-733">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log  ) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout  ）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="8f17a-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="8f17a-734">如果 `stdoutLogFile` 路径以 stdout  结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="8f17a-735">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="8f17a-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="8f17a-736">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="8f17a-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="8f17a-737">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="8f17a-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="8f17a-738">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="8f17a-739">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="8f17a-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="8f17a-740">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="8f17a-741">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="8f17a-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="8f17a-742">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="8f17a-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="8f17a-743">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="8f17a-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="8f17a-744">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="8f17a-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="8f17a-745">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="8f17a-746">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="8f17a-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="8f17a-747">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="8f17a-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="8f17a-748">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="8f17a-749">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="8f17a-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="8f17a-750">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="8f17a-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="8f17a-751">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="8f17a-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="8f17a-752">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="8f17a-753">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="8f17a-754">使用 IIS 共享配置时，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="8f17a-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="8f17a-755">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="8f17a-756">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="8f17a-756">Run the installer.</span></span>
1. <span data-ttu-id="8f17a-757">将已更新的 applicationHost.config  文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="8f17a-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="8f17a-758">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="8f17a-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="8f17a-759">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="8f17a-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="8f17a-760">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8f17a-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="8f17a-761">在托管系统上，导航到 %windir%\System32\inetsrv  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="8f17a-762">找到 aspnetcore.dll  文件。</span><span class="sxs-lookup"><span data-stu-id="8f17a-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="8f17a-763">右键单击该文件，然后从上下文菜单中选择“属性”  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="8f17a-764">选择“详细信息”选项卡  。“文件版本”  和“产品版本”  表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="8f17a-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="8f17a-765">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp  中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log  。</span><span class="sxs-lookup"><span data-stu-id="8f17a-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="8f17a-766">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="8f17a-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="8f17a-767">模块</span><span class="sxs-lookup"><span data-stu-id="8f17a-767">Module</span></span>

<span data-ttu-id="8f17a-768">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8f17a-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="8f17a-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="8f17a-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="8f17a-771">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="8f17a-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="8f17a-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="8f17a-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="8f17a-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="8f17a-774">架构</span><span class="sxs-lookup"><span data-stu-id="8f17a-774">Schema</span></span>

<span data-ttu-id="8f17a-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8f17a-775">**IIS**</span></span>

* <span data-ttu-id="8f17a-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8f17a-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="8f17a-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8f17a-777">**IIS Express**</span></span>

* <span data-ttu-id="8f17a-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="8f17a-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="8f17a-779">Configuration</span><span class="sxs-lookup"><span data-stu-id="8f17a-779">Configuration</span></span>

<span data-ttu-id="8f17a-780">**IIS**</span><span class="sxs-lookup"><span data-stu-id="8f17a-780">**IIS**</span></span>

* <span data-ttu-id="8f17a-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8f17a-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="8f17a-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="8f17a-782">**IIS Express**</span></span>

* <span data-ttu-id="8f17a-783">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="8f17a-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="8f17a-784"> iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="8f17a-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="8f17a-785">可以通过在 applicationHost.config  文件中搜索 aspnetcore  找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="8f17a-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8f17a-786">其他资源</span><span class="sxs-lookup"><span data-stu-id="8f17a-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="8f17a-787">[ASP.NET Core 模块引用源（主分支）](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; 使用“分支”  下拉列表选择特定版本（例如，`release/3.1`）。</span><span class="sxs-lookup"><span data-stu-id="8f17a-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
