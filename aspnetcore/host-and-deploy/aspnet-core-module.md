---
<span data-ttu-id="5d565-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-102">'Blazor'</span></span>
- <span data-ttu-id="5d565-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-103">'Identity'</span></span>
- <span data-ttu-id="5d565-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-105">'Razor'</span></span>
- <span data-ttu-id="5d565-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-module"></a><span data-ttu-id="5d565-107">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5d565-107">ASP.NET Core Module</span></span>

<span data-ttu-id="5d565-108">作者：[Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti) 和 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="5d565-108">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d565-109">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="5d565-109">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="5d565-110">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="5d565-110">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="5d565-111">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="5d565-111">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="5d565-112">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="5d565-112">Supported Windows versions:</span></span>

* <span data-ttu-id="5d565-113">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5d565-113">Windows 7 or later</span></span>
* <span data-ttu-id="5d565-114">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5d565-114">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5d565-115">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-115">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5d565-116">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5d565-116">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="5d565-117">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="5d565-117">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5d565-118">托管模型</span><span class="sxs-lookup"><span data-stu-id="5d565-118">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5d565-119">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="5d565-119">In-process hosting model</span></span>

<span data-ttu-id="5d565-120">ASP.NET Core 应用默认为进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="5d565-120">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="5d565-121">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="5d565-121">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="5d565-122">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="5d565-122">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="5d565-123">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5d565-123">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="5d565-124">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="5d565-124">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="5d565-125">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-125">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="5d565-126">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="5d565-126">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="5d565-127">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="5d565-127">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="5d565-128">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="5d565-128">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="5d565-129">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="5d565-129">Use one app pool per app.</span></span>

* <span data-ttu-id="5d565-130">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="5d565-130">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5d565-131">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="5d565-131">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="5d565-132">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="5d565-132">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="5d565-133">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="5d565-133">Client disconnects are detected.</span></span> <span data-ttu-id="5d565-134">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="5d565-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="5d565-135">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv ）。</span><span class="sxs-lookup"><span data-stu-id="5d565-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="5d565-136">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="5d565-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="5d565-137">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="5d565-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="5d565-138">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="5d565-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="5d565-139">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="5d565-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5d565-140">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="5d565-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5d565-141">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="5d565-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="5d565-142">不支持 [Web 包（单文件）部署](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)。</span><span class="sxs-lookup"><span data-stu-id="5d565-142">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5d565-143">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="5d565-143">Out-of-process hosting model</span></span>

<span data-ttu-id="5d565-144">若要配置进程外托管应用，请在项目文件 ( *.csproj*) 中将 `<AspNetCoreHostingModel>` 属性的值设置为 `OutOfProcess`：</span><span class="sxs-lookup"><span data-stu-id="5d565-144">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5d565-145">进程内托管设为 `InProcess`，这是默认值。</span><span class="sxs-lookup"><span data-stu-id="5d565-145">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="5d565-146">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="5d565-146">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="5d565-147">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5d565-148">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5d565-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="5d565-149">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="5d565-150">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="5d565-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="5d565-151">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="5d565-151">Hosting model changes</span></span>

<span data-ttu-id="5d565-152">如果 `hostingModel` 设置在 web.config 文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="5d565-153">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="5d565-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="5d565-154">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="5d565-155">进程名</span><span class="sxs-lookup"><span data-stu-id="5d565-155">Process name</span></span>

<span data-ttu-id="5d565-156">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="5d565-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="5d565-157">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="5d565-157">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="5d565-158">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="5d565-158">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="5d565-159">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="5d565-159">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="5d565-160">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-160">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="5d565-161">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="5d565-161">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="5d565-162">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="5d565-162">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="5d565-163">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5d565-163">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="5d565-164">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="5d565-164">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="5d565-165">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="5d565-165">Configuration with web.config</span></span>

<span data-ttu-id="5d565-166">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="5d565-166">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="5d565-167">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="5d565-167">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="5d565-168">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="5d565-168">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="5d565-169">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="5d565-169">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="5d565-170">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="5d565-170">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5d565-171">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="5d565-171">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="5d565-172">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="5d565-172">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="5d565-173">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="5d565-173">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="5d565-174">特性</span><span class="sxs-lookup"><span data-stu-id="5d565-174">Attribute</span></span> | <span data-ttu-id="5d565-175">描述</span><span class="sxs-lookup"><span data-stu-id="5d565-175">Description</span></span> | <span data-ttu-id="5d565-176">默认</span><span class="sxs-lookup"><span data-stu-id="5d565-176">Default</span></span> |
| ---
<span data-ttu-id="5d565-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-178">'Blazor'</span></span>
- <span data-ttu-id="5d565-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-179">'Identity'</span></span>
- <span data-ttu-id="5d565-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-181">'Razor'</span></span>
- <span data-ttu-id="5d565-182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5d565-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-184">'Blazor'</span></span>
- <span data-ttu-id="5d565-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-185">'Identity'</span></span>
- <span data-ttu-id="5d565-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-187">'Razor'</span></span>
- <span data-ttu-id="5d565-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-188">'SignalR' uid:</span></span> 

<span data-ttu-id="5d565-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-190">'Blazor'</span></span>
- <span data-ttu-id="5d565-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-191">'Identity'</span></span>
- <span data-ttu-id="5d565-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-193">'Razor'</span></span>
- <span data-ttu-id="5d565-194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5d565-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-196">'Blazor'</span></span>
- <span data-ttu-id="5d565-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-197">'Identity'</span></span>
- <span data-ttu-id="5d565-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-199">'Razor'</span></span>
- <span data-ttu-id="5d565-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5d565-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-202">'Blazor'</span></span>
- <span data-ttu-id="5d565-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-203">'Identity'</span></span>
- <span data-ttu-id="5d565-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-205">'Razor'</span></span>
- <span data-ttu-id="5d565-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-206">'SignalR' uid:</span></span> 

<span data-ttu-id="5d565-207">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="5d565-207">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="5d565-208">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-208">Optional string attribute.</span></span></p><p><span data-ttu-id="5d565-209">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="5d565-209">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="5d565-210">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="5d565-210">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="5d565-211">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-211">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5d565-212">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="5d565-212">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="5d565-213">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="5d565-213">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="5d565-214">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-214">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5d565-215">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-215">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5d565-216">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="5d565-216">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="5d565-217">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="5d565-217">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="5d565-218">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-218">Optional string attribute.</span></span></p><p><span data-ttu-id="5d565-219">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-219">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br><span data-ttu-id="5d565-220">`inprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="5d565-220">`inprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="5d565-221">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-221">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-222">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="5d565-222">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5d565-223">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="5d565-223">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="5d565-224">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="5d565-224">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5d565-225">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-225">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="5d565-226">| 默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="5d565-226">| Default: `1`</span></span><br><span data-ttu-id="5d565-227">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="5d565-227">Min: `1`</span></span><br><span data-ttu-id="5d565-228">最大值：`100` | | &dagger;`processPath` | </span><span class="sxs-lookup"><span data-stu-id="5d565-228">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="5d565-229">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-229">Required string attribute.</span></span></p><p><span data-ttu-id="5d565-230">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-230">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5d565-231">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-231">Relative paths are supported.</span></span> <span data-ttu-id="5d565-232">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5d565-232">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="5d565-233">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="5d565-233">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="5d565-234">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-235">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="5d565-235">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5d565-236">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-236">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="5d565-237">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="5d565-237">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="5d565-238">| 默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5d565-238">| Default: `10`</span></span><br><span data-ttu-id="5d565-239">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5d565-239">Min: `0`</span></span><br><span data-ttu-id="5d565-240">最大值：`100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="5d565-240">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="5d565-241">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-241">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5d565-242">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="5d565-242">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5d565-243">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="5d565-243">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="5d565-244">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="5d565-244">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="5d565-245">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-245">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="5d565-246">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="5d565-246">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="5d565-247">在分钟或秒钟值中使用“60”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="5d565-247">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="5d565-248">| 默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5d565-248">| Default: `00:02:00`</span></span><br><span data-ttu-id="5d565-249">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5d565-249">Min: `00:00:00`</span></span><br><span data-ttu-id="5d565-250">最大值：`360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="5d565-250">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="5d565-251">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-251">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-252">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5d565-252">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="5d565-253">| 默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5d565-253">| Default: `10`</span></span><br><span data-ttu-id="5d565-254">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5d565-254">Min: `0`</span></span><br><span data-ttu-id="5d565-255">最大值：`600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="5d565-255">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="5d565-256">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-256">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-257">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5d565-257">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5d565-258">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-258">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="5d565-259">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="5d565-259">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5d565-260">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="5d565-260">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="5d565-261">| 默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="5d565-261">| Default: `120`</span></span><br><span data-ttu-id="5d565-262">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5d565-262">Min: `0`</span></span><br><span data-ttu-id="5d565-263">最大值：`3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="5d565-263">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="5d565-264">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-264">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5d565-265">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="5d565-265">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="5d565-266">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="5d565-266">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="5d565-267">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-267">Optional string attribute.</span></span></p><p><span data-ttu-id="5d565-268">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-268">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5d565-269">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5d565-269">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5d565-270">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-270">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5d565-271">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="5d565-271">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="5d565-272">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="5d565-272">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5d565-273">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="5d565-273">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="5d565-274">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="5d565-274">Set environment variables</span></span>

<span data-ttu-id="5d565-275">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-275">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5d565-276">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-276">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="5d565-277">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-277">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="5d565-278">以下示例在 web.config 中设置两个环境变量。`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="5d565-278">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="5d565-279">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="5d565-279">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="5d565-280">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-280">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="5d565-281">直接在 web.config 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="5d565-281">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="5d565-282">此方法在发布项目时设置 web.config 中的环境：</span><span class="sxs-lookup"><span data-stu-id="5d565-282">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="5d565-283">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="5d565-283">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="5d565-284">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="5d565-284">app_offline.htm</span></span>

<span data-ttu-id="5d565-285">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-285">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="5d565-286">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-286">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="5d565-287">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-287">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="5d565-288">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="5d565-288">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="5d565-289">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="5d565-289">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5d565-290">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="5d565-290">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="5d565-291">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="5d565-291">Start-up error page</span></span>

<span data-ttu-id="5d565-292">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="5d565-292">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="5d565-293">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5d565-293">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="5d565-294">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5d565-294">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="5d565-295">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5d565-295">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="5d565-296">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-296">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5d565-297">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="5d565-297">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="5d565-298">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="5d565-298">Log creation and redirection</span></span>

<span data-ttu-id="5d565-299">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="5d565-299">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="5d565-300">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="5d565-300">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="5d565-301">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="5d565-301">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5d565-302">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-302">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="5d565-303">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="5d565-303">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="5d565-304">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="5d565-304">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="5d565-305">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="5d565-305">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="5d565-306">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="5d565-306">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5d565-307">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="5d565-307">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="5d565-308">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="5d565-308">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="5d565-309">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="5d565-309">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="5d565-310">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="5d565-310">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="5d565-311">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="5d565-311">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="5d565-312">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="5d565-312">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="5d565-313">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="5d565-313">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="5d565-314">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="5d565-314">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="5d565-315">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="5d565-315">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5d565-316">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-316">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="5d565-317">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="5d565-317">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="5d565-318">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="5d565-318">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="5d565-319">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="5d565-319">Enhanced diagnostic logs</span></span>

<span data-ttu-id="5d565-320">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="5d565-320">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="5d565-321">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="5d565-321">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="5d565-322">创建日志文件时，路径中的任何文件夹（上述示例中为 logs）由模块创建。</span><span class="sxs-lookup"><span data-stu-id="5d565-322">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="5d565-323">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="5d565-323">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5d565-324">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="5d565-324">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="5d565-325">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="5d565-325">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="5d565-326">错误</span><span class="sxs-lookup"><span data-stu-id="5d565-326">ERROR</span></span>
* <span data-ttu-id="5d565-327">警告</span><span class="sxs-lookup"><span data-stu-id="5d565-327">WARNING</span></span>
* <span data-ttu-id="5d565-328">信息</span><span class="sxs-lookup"><span data-stu-id="5d565-328">INFO</span></span>
* <span data-ttu-id="5d565-329">TRACE</span><span class="sxs-lookup"><span data-stu-id="5d565-329">TRACE</span></span>

<span data-ttu-id="5d565-330">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="5d565-330">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="5d565-331">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="5d565-331">CONSOLE</span></span>
* <span data-ttu-id="5d565-332">事件日志</span><span class="sxs-lookup"><span data-stu-id="5d565-332">EVENTLOG</span></span>
* <span data-ttu-id="5d565-333">文件</span><span class="sxs-lookup"><span data-stu-id="5d565-333">FILE</span></span>

<span data-ttu-id="5d565-334">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="5d565-334">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="5d565-335">`ASPNETCORE_MODULE_DEBUG_FILE`：调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-335">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="5d565-336">（默认值：aspnetcore debug.log）</span><span class="sxs-lookup"><span data-stu-id="5d565-336">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="5d565-337">`ASPNETCORE_MODULE_DEBUG`：调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="5d565-337">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="5d565-338">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="5d565-338">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="5d565-339">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="5d565-339">The size of the log isn't limited.</span></span> <span data-ttu-id="5d565-340">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="5d565-340">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="5d565-341">有关 web.config 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="5d565-341">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="5d565-342">修改堆栈大小</span><span class="sxs-lookup"><span data-stu-id="5d565-342">Modify the stack size</span></span>

<span data-ttu-id="5d565-343">*仅适用于使用进程内托管模型的情况。*</span><span class="sxs-lookup"><span data-stu-id="5d565-343">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="5d565-344">使用 web.config 中的 `stackSize` 设置（以字节为单位）配置托管堆栈大小。默认大小为 `1048576` 个字节 (1 MB)。</span><span class="sxs-lookup"><span data-stu-id="5d565-344">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="5d565-345">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="5d565-345">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="5d565-346">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="5d565-346">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="5d565-347">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="5d565-347">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="5d565-348">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="5d565-348">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="5d565-349">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="5d565-349">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="5d565-350">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-350">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="5d565-351">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-351">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="5d565-352">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="5d565-352">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="5d565-353">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-353">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="5d565-354">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="5d565-354">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="5d565-355">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-355">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="5d565-356">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5d565-356">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="5d565-357">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="5d565-357">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="5d565-358">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="5d565-358">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="5d565-359">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="5d565-359">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="5d565-360">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="5d565-360">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="5d565-361">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="5d565-361">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5d565-362">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="5d565-362">Run the installer.</span></span>
1. <span data-ttu-id="5d565-363">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="5d565-363">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5d565-364">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="5d565-364">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="5d565-365">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="5d565-365">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="5d565-366">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5d565-366">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="5d565-367">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="5d565-367">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="5d565-368">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="5d565-368">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="5d565-369">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="5d565-369">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="5d565-370">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="5d565-370">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="5d565-371">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="5d565-371">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="5d565-372">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="5d565-372">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="5d565-373">模块</span><span class="sxs-lookup"><span data-stu-id="5d565-373">Module</span></span>

<span data-ttu-id="5d565-374">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="5d565-374">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="5d565-375">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-375">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5d565-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5d565-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5d565-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="5d565-379">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="5d565-379">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="5d565-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5d565-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5d565-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5d565-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="5d565-384">架构</span><span class="sxs-lookup"><span data-stu-id="5d565-384">Schema</span></span>

<span data-ttu-id="5d565-385">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5d565-385">**IIS**</span></span>

* <span data-ttu-id="5d565-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5d565-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5d565-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5d565-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="5d565-388">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5d565-388">**IIS Express**</span></span>

* <span data-ttu-id="5d565-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5d565-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5d565-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5d565-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="5d565-391">Configuration</span><span class="sxs-lookup"><span data-stu-id="5d565-391">Configuration</span></span>

<span data-ttu-id="5d565-392">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5d565-392">**IIS**</span></span>

* <span data-ttu-id="5d565-393">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5d565-393">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="5d565-394">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5d565-394">**IIS Express**</span></span>

* <span data-ttu-id="5d565-395">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5d565-395">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="5d565-396">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="5d565-396">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="5d565-397">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="5d565-397">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5d565-398">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于：</span><span class="sxs-lookup"><span data-stu-id="5d565-398">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="5d565-399">在 IIS 工作进程 (`w3wp.exe`) 内托管 ASP.NET Core 应用，称为[进程内托管模型](#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="5d565-399">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="5d565-400">将 Web 请求转发到运行 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的后端 ASP.NET Core 应用，称为[进程外托管模型](#out-of-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="5d565-400">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="5d565-401">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="5d565-401">Supported Windows versions:</span></span>

* <span data-ttu-id="5d565-402">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5d565-402">Windows 7 or later</span></span>
* <span data-ttu-id="5d565-403">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5d565-403">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5d565-404">在进程内托管时，该模块会使用 IIS 进程内服务器实现，即 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-404">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5d565-405">在进程外托管时，该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5d565-405">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="5d565-406">该模块无法与 [HTTP.sys](xref:fundamentals/servers/httpsys) 一起工作。</span><span class="sxs-lookup"><span data-stu-id="5d565-406">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="5d565-407">托管模型</span><span class="sxs-lookup"><span data-stu-id="5d565-407">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="5d565-408">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="5d565-408">In-process hosting model</span></span>

<span data-ttu-id="5d565-409">若要配置用于进程内托管的应用，请将 `<AspNetCoreHostingModel>` 属性添加到值为 `InProcess`（进程外托管使用 `OutOfProcess` 进行设置）的应用项目文件：</span><span class="sxs-lookup"><span data-stu-id="5d565-409">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5d565-410">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="5d565-410">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="5d565-411">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="5d565-411">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="5d565-412">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="5d565-412">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="5d565-413">在进程内托管时，将应用以下特征：</span><span class="sxs-lookup"><span data-stu-id="5d565-413">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="5d565-414">使用 IIS HTTP 服务器 (`IISHttpServer`) 而不是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器。</span><span class="sxs-lookup"><span data-stu-id="5d565-414">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="5d565-415">在进程内，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5d565-415">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="5d565-416">注册 `IISHttpServer`。</span><span class="sxs-lookup"><span data-stu-id="5d565-416">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="5d565-417">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-417">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="5d565-418">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="5d565-418">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="5d565-419">[requestTimeout 属性](#attributes-of-the-aspnetcore-element)不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="5d565-419">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="5d565-420">不支持在应用之间共享应用池。</span><span class="sxs-lookup"><span data-stu-id="5d565-420">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="5d565-421">每个应用使用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="5d565-421">Use one app pool per app.</span></span>

* <span data-ttu-id="5d565-422">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)或手动将 [app_offline.htm 文件置于部署中](xref:host-and-deploy/iis/index#locked-deployment-files)时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="5d565-422">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5d565-423">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="5d565-423">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="5d565-424">应用和已安装的运行时（x64 或 x86）的体系结构（位数）必须与应用池的体系结构匹配。</span><span class="sxs-lookup"><span data-stu-id="5d565-424">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="5d565-425">检测到客户端连接断开。</span><span class="sxs-lookup"><span data-stu-id="5d565-425">Client disconnects are detected.</span></span> <span data-ttu-id="5d565-426">客户端连接断开时，[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 取消标记将会取消。</span><span class="sxs-lookup"><span data-stu-id="5d565-426">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="5d565-427">在 ASP.NET Core 2.2.1 或早期版本中，<xref:System.IO.Directory.GetCurrentDirectory*> 会返回 IIS 启动的进程的工作目录而非应用目录（例如，对于 w3wp.exe，是 C:\Windows\System32\inetsrv ）。</span><span class="sxs-lookup"><span data-stu-id="5d565-427">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="5d565-428">对于设置应用的当前目录的示例代码，请参阅 [CurrentDirectoryHelpers 类](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)。</span><span class="sxs-lookup"><span data-stu-id="5d565-428">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="5d565-429">调用 `SetCurrentDirectory` 方法。</span><span class="sxs-lookup"><span data-stu-id="5d565-429">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="5d565-430">后续 <xref:System.IO.Directory.GetCurrentDirectory*> 调用提供应用的目录。</span><span class="sxs-lookup"><span data-stu-id="5d565-430">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="5d565-431">在进程内托管时，不会在内部调用 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="5d565-431">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5d565-432">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="5d565-432">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5d565-433">使用 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现转换声明时，请调用 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 以添加身份验证服务：</span><span class="sxs-lookup"><span data-stu-id="5d565-433">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="5d565-434">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="5d565-434">Out-of-process hosting model</span></span>

<span data-ttu-id="5d565-435">若要配置进程外托管应用，请在项目文件中使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="5d565-435">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="5d565-436">不指定 `<AspNetCoreHostingModel>` 属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-436">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="5d565-437">如果文件中不存在 `<AspNetCoreHostingModel>` 属性，则默认值为 `OutOfProcess`。</span><span class="sxs-lookup"><span data-stu-id="5d565-437">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="5d565-438">将 `<AspNetCoreHostingModel>` 属性的值设为 `OutOfProcess`（进程内托管设为 `InProcess`）：</span><span class="sxs-lookup"><span data-stu-id="5d565-438">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="5d565-439">值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="5d565-439">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="5d565-440">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-440">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="5d565-441">在进程外，[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5d565-441">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="5d565-442">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-442">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="5d565-443">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="5d565-443">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="5d565-444">托管模型更改</span><span class="sxs-lookup"><span data-stu-id="5d565-444">Hosting model changes</span></span>

<span data-ttu-id="5d565-445">如果 `hostingModel` 设置在 web.config 文件中被更改（如 [web.config 的配置](#configuration-with-webconfig)部分中所述），则该模块会再循环 IIS 工作进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-445">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="5d565-446">对于 IIS Express，该模块不会再循环工作进程，但改为触发当前 IIS Express 进程的正常关闭。</span><span class="sxs-lookup"><span data-stu-id="5d565-446">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="5d565-447">应用的下一个请求会生成新的 IIS Express 进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-447">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="5d565-448">进程名</span><span class="sxs-lookup"><span data-stu-id="5d565-448">Process name</span></span>

<span data-ttu-id="5d565-449">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="5d565-449">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="5d565-450">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="5d565-450">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="5d565-451">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="5d565-451">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="5d565-452">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="5d565-452">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="5d565-453">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-453">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="5d565-454">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="5d565-454">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="5d565-455">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="5d565-455">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="5d565-456">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5d565-456">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="5d565-457">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="5d565-457">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="5d565-458">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="5d565-458">Configuration with web.config</span></span>

<span data-ttu-id="5d565-459">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="5d565-459">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="5d565-460">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="5d565-460">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="5d565-461">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="5d565-461">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="5d565-462">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="5d565-462">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="5d565-463">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="5d565-463">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5d565-464">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="5d565-464">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="5d565-465">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="5d565-465">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="5d565-466">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="5d565-466">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="5d565-467">特性</span><span class="sxs-lookup"><span data-stu-id="5d565-467">Attribute</span></span> | <span data-ttu-id="5d565-468">描述</span><span class="sxs-lookup"><span data-stu-id="5d565-468">Description</span></span> | <span data-ttu-id="5d565-469">默认</span><span class="sxs-lookup"><span data-stu-id="5d565-469">Default</span></span> |
| ---
<span data-ttu-id="5d565-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-471">'Blazor'</span></span>
- <span data-ttu-id="5d565-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-472">'Identity'</span></span>
- <span data-ttu-id="5d565-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-474">'Razor'</span></span>
- <span data-ttu-id="5d565-475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5d565-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-477">'Blazor'</span></span>
- <span data-ttu-id="5d565-478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-478">'Identity'</span></span>
- <span data-ttu-id="5d565-479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-479">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-480">'Razor'</span></span>
- <span data-ttu-id="5d565-481">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-481">'SignalR' uid:</span></span> 

<span data-ttu-id="5d565-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-483">'Blazor'</span></span>
- <span data-ttu-id="5d565-484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-484">'Identity'</span></span>
- <span data-ttu-id="5d565-485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-485">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-486">'Razor'</span></span>
- <span data-ttu-id="5d565-487">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5d565-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-489">'Blazor'</span></span>
- <span data-ttu-id="5d565-490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-490">'Identity'</span></span>
- <span data-ttu-id="5d565-491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-491">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-492">'Razor'</span></span>
- <span data-ttu-id="5d565-493">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5d565-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-495">'Blazor'</span></span>
- <span data-ttu-id="5d565-496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-496">'Identity'</span></span>
- <span data-ttu-id="5d565-497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-497">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-498">'Razor'</span></span>
- <span data-ttu-id="5d565-499">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-499">'SignalR' uid:</span></span> 

<span data-ttu-id="5d565-500">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="5d565-500">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="5d565-501">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-501">Optional string attribute.</span></span></p><p><span data-ttu-id="5d565-502">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="5d565-502">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="5d565-503">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="5d565-503">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="5d565-504">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-504">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5d565-505">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="5d565-505">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="5d565-506">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="5d565-506">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="5d565-507">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-507">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5d565-508">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-508">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5d565-509">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="5d565-509">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="5d565-510">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="5d565-510">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="5d565-511">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-511">Optional string attribute.</span></span></p><p><span data-ttu-id="5d565-512">将托管模型指定为进程内 (`InProcess`/`inprocess`) 或进程外 (`OutOfProcess`/`outofprocess`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-512">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br><span data-ttu-id="5d565-513">`outofprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="5d565-513">`outofprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="5d565-514">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-514">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-515">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="5d565-515">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5d565-516">&dagger;对于进程内托管，值限制为 `1`。</span><span class="sxs-lookup"><span data-stu-id="5d565-516">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="5d565-517">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="5d565-517">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5d565-518">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-518">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="5d565-519">| 默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="5d565-519">| Default: `1`</span></span><br><span data-ttu-id="5d565-520">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="5d565-520">Min: `1`</span></span><br><span data-ttu-id="5d565-521">最大值：`100` | | &dagger;`processPath` | </span><span class="sxs-lookup"><span data-stu-id="5d565-521">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="5d565-522">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-522">Required string attribute.</span></span></p><p><span data-ttu-id="5d565-523">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-523">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5d565-524">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-524">Relative paths are supported.</span></span> <span data-ttu-id="5d565-525">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5d565-525">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="5d565-526">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="5d565-526">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="5d565-527">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-527">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-528">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="5d565-528">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5d565-529">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-529">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="5d565-530">不支持进程内托管。</span><span class="sxs-lookup"><span data-stu-id="5d565-530">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="5d565-531">| 默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5d565-531">| Default: `10`</span></span><br><span data-ttu-id="5d565-532">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5d565-532">Min: `0`</span></span><br><span data-ttu-id="5d565-533">最大值：`100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="5d565-533">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="5d565-534">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-534">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5d565-535">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="5d565-535">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5d565-536">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="5d565-536">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="5d565-537">不适用于进程内托管。</span><span class="sxs-lookup"><span data-stu-id="5d565-537">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="5d565-538">对于进程内托管，该模块等待应用处理该请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-538">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="5d565-539">此字符串的分钟段和秒钟段的有效值在 0-59 之间。</span><span class="sxs-lookup"><span data-stu-id="5d565-539">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="5d565-540">在分钟或秒钟值中使用“60”会导致“500 - 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="5d565-540">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="5d565-541">| 默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5d565-541">| Default: `00:02:00`</span></span><br><span data-ttu-id="5d565-542">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5d565-542">Min: `00:00:00`</span></span><br><span data-ttu-id="5d565-543">最大值：`360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="5d565-543">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="5d565-544">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-544">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-545">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5d565-545">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="5d565-546">| 默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5d565-546">| Default: `10`</span></span><br><span data-ttu-id="5d565-547">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5d565-547">Min: `0`</span></span><br><span data-ttu-id="5d565-548">最大值：`600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="5d565-548">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="5d565-549">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-549">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-550">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5d565-550">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5d565-551">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-551">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="5d565-552">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="5d565-552">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5d565-553">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="5d565-553">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="5d565-554">| 默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="5d565-554">| Default: `120`</span></span><br><span data-ttu-id="5d565-555">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5d565-555">Min: `0`</span></span><br><span data-ttu-id="5d565-556">最大值：`3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="5d565-556">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="5d565-557">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-557">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5d565-558">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="5d565-558">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="5d565-559">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="5d565-559">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="5d565-560">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-560">Optional string attribute.</span></span></p><p><span data-ttu-id="5d565-561">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-561">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5d565-562">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5d565-562">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5d565-563">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-563">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5d565-564">创建日志文件时，模块会创建路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="5d565-564">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="5d565-565">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="5d565-565">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5d565-566">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="5d565-566">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="5d565-567">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="5d565-567">Setting environment variables</span></span>

<span data-ttu-id="5d565-568">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-568">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5d565-569">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-569">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="5d565-570">本部分中设置的环境变量优先于系统环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-570">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="5d565-571">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-571">The following example sets two environment variables.</span></span> <span data-ttu-id="5d565-572">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="5d565-572">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="5d565-573">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="5d565-573">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="5d565-574">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-574">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="5d565-575">直接在 web.config 中设置环境的替代方法是将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。</span><span class="sxs-lookup"><span data-stu-id="5d565-575">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="5d565-576">此方法在发布项目时设置 web.config 中的环境：</span><span class="sxs-lookup"><span data-stu-id="5d565-576">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="5d565-577">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="5d565-577">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="5d565-578">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="5d565-578">app_offline.htm</span></span>

<span data-ttu-id="5d565-579">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-579">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="5d565-580">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-580">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="5d565-581">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-581">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="5d565-582">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="5d565-582">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="5d565-583">使用进程外托管模型时，如果有已打开的连接，则应用可能不会立即关闭。</span><span class="sxs-lookup"><span data-stu-id="5d565-583">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="5d565-584">例如，WebSocket 连接可能会延迟应用关闭。</span><span class="sxs-lookup"><span data-stu-id="5d565-584">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="5d565-585">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="5d565-585">Start-up error page</span></span>

<span data-ttu-id="5d565-586">进程内和进程外托管在无法启动应用时均会生成自定义错误页面。</span><span class="sxs-lookup"><span data-stu-id="5d565-586">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="5d565-587">如果 ASP.NET Core 模块无法找到进程内或进程外请求处理程序，则会显示“500.0 - 进程内/进程外处理程序加载失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5d565-587">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="5d565-588">对于进程内托管，如果 ASP.NET Core 模块无法启动应用，则会显示“500.30 - 启动失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5d565-588">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="5d565-589">对于进程外托管，如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5d565-589">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="5d565-590">若要禁止显示此页面并还原为默认 IIS 5xx 状态代码页，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-590">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5d565-591">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="5d565-591">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="5d565-592">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="5d565-592">Log creation and redirection</span></span>

<span data-ttu-id="5d565-593">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="5d565-593">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="5d565-594">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="5d565-594">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="5d565-595">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="5d565-595">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5d565-596">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-596">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="5d565-597">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="5d565-597">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="5d565-598">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="5d565-598">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="5d565-599">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="5d565-599">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="5d565-600">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="5d565-600">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5d565-601">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="5d565-601">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="5d565-602">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="5d565-602">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="5d565-603">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="5d565-603">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="5d565-604">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="5d565-604">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="5d565-605">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="5d565-605">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="5d565-606">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="5d565-606">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="5d565-607">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="5d565-607">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="5d565-608">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="5d565-608">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="5d565-609">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="5d565-609">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5d565-610">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-610">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="5d565-611">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="5d565-611">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="5d565-612">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="5d565-612">Enhanced diagnostic logs</span></span>

<span data-ttu-id="5d565-613">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="5d565-613">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="5d565-614">向 web.config 中的 `<aspNetCore>` 元素添加 `<handlerSettings>` 元素。将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="5d565-614">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="5d565-615">提供给 `<handlerSetting>` 值（上述示例中为 logs）的路径中的文件夹不是由模块自动创建的，并且应该预先存在于部署中。</span><span class="sxs-lookup"><span data-stu-id="5d565-615">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="5d565-616">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="5d565-616">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5d565-617">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="5d565-617">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="5d565-618">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="5d565-618">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="5d565-619">错误</span><span class="sxs-lookup"><span data-stu-id="5d565-619">ERROR</span></span>
* <span data-ttu-id="5d565-620">警告</span><span class="sxs-lookup"><span data-stu-id="5d565-620">WARNING</span></span>
* <span data-ttu-id="5d565-621">信息</span><span class="sxs-lookup"><span data-stu-id="5d565-621">INFO</span></span>
* <span data-ttu-id="5d565-622">TRACE</span><span class="sxs-lookup"><span data-stu-id="5d565-622">TRACE</span></span>

<span data-ttu-id="5d565-623">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="5d565-623">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="5d565-624">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="5d565-624">CONSOLE</span></span>
* <span data-ttu-id="5d565-625">事件日志</span><span class="sxs-lookup"><span data-stu-id="5d565-625">EVENTLOG</span></span>
* <span data-ttu-id="5d565-626">文件</span><span class="sxs-lookup"><span data-stu-id="5d565-626">FILE</span></span>

<span data-ttu-id="5d565-627">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="5d565-627">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="5d565-628">`ASPNETCORE_MODULE_DEBUG_FILE`：调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-628">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="5d565-629">（默认值：aspnetcore debug.log）</span><span class="sxs-lookup"><span data-stu-id="5d565-629">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="5d565-630">`ASPNETCORE_MODULE_DEBUG`：调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="5d565-630">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="5d565-631">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="5d565-631">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="5d565-632">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="5d565-632">The size of the log isn't limited.</span></span> <span data-ttu-id="5d565-633">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="5d565-633">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="5d565-634">有关 web.config 文件中的 `aspNetCore` 元素的示例，请参阅 [web.config 的配置](#configuration-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="5d565-634">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="5d565-635">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="5d565-635">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="5d565-636">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="5d565-636">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="5d565-637">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="5d565-637">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="5d565-638">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="5d565-638">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="5d565-639">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="5d565-639">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="5d565-640">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-640">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="5d565-641">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-641">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="5d565-642">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="5d565-642">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="5d565-643">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-643">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="5d565-644">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="5d565-644">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="5d565-645">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-645">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="5d565-646">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5d565-646">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="5d565-647">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="5d565-647">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="5d565-648">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="5d565-648">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="5d565-649">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="5d565-649">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="5d565-650">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="5d565-650">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="5d565-651">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="5d565-651">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5d565-652">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="5d565-652">Run the installer.</span></span>
1. <span data-ttu-id="5d565-653">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="5d565-653">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5d565-654">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="5d565-654">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="5d565-655">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="5d565-655">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="5d565-656">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5d565-656">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="5d565-657">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="5d565-657">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="5d565-658">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="5d565-658">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="5d565-659">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="5d565-659">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="5d565-660">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="5d565-660">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="5d565-661">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="5d565-661">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="5d565-662">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="5d565-662">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="5d565-663">模块</span><span class="sxs-lookup"><span data-stu-id="5d565-663">Module</span></span>

<span data-ttu-id="5d565-664">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="5d565-664">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="5d565-665">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-665">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5d565-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5d565-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5d565-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="5d565-669">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="5d565-669">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="5d565-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5d565-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5d565-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="5d565-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="5d565-674">架构</span><span class="sxs-lookup"><span data-stu-id="5d565-674">Schema</span></span>

<span data-ttu-id="5d565-675">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5d565-675">**IIS**</span></span>

* <span data-ttu-id="5d565-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5d565-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5d565-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5d565-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="5d565-678">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5d565-678">**IIS Express**</span></span>

* <span data-ttu-id="5d565-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5d565-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="5d565-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="5d565-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="5d565-681">Configuration</span><span class="sxs-lookup"><span data-stu-id="5d565-681">Configuration</span></span>

<span data-ttu-id="5d565-682">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5d565-682">**IIS**</span></span>

* <span data-ttu-id="5d565-683">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5d565-683">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="5d565-684">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5d565-684">**IIS Express**</span></span>

* <span data-ttu-id="5d565-685">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5d565-685">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="5d565-686">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="5d565-686">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="5d565-687">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="5d565-687">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5d565-688">ASP.NET Core 模块是插入 IIS 管道的本机 IIS 模块，用于将 Web 请求转发到后端 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="5d565-688">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="5d565-689">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="5d565-689">Supported Windows versions:</span></span>

* <span data-ttu-id="5d565-690">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5d565-690">Windows 7 or later</span></span>
* <span data-ttu-id="5d565-691">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5d565-691">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="5d565-692">该模块仅适用于 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5d565-692">The module only works with Kestrel.</span></span> <span data-ttu-id="5d565-693">该模块与 [HTTP.sys](xref:fundamentals/servers/httpsys) 不兼容。</span><span class="sxs-lookup"><span data-stu-id="5d565-693">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="5d565-694">由于 ASP.NET Core 应用在独立于 IIS 辅助进程的进程中运行，因此该模块还处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="5d565-694">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="5d565-695">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="5d565-695">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="5d565-696">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的 IIS 中在进程内运行的 ASP.NET 4.x 应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="5d565-696">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="5d565-697">下图说明了 IIS、ASP.NET Core 模块和应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="5d565-697">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 模块](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="5d565-699">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="5d565-699">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="5d565-700">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="5d565-700">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="5d565-701">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5d565-701">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="5d565-702">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="5d565-702">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="5d565-703">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-703">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="5d565-704">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="5d565-704">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="5d565-705">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="5d565-705">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="5d565-706">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="5d565-706">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="5d565-707">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="5d565-707">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="5d565-708">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="5d565-708">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="5d565-709">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="5d565-709">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="5d565-710">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="5d565-710">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="5d565-711">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="5d565-711">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="5d565-712">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-712">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="5d565-713">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="5d565-713">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="5d565-714">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="5d565-714">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="5d565-715">如何安装和使用 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5d565-715">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="5d565-716">有关如何安装 ASP.NET Core 模块的说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="5d565-716">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="5d565-717">web.config 的配置</span><span class="sxs-lookup"><span data-stu-id="5d565-717">Configuration with web.config</span></span>

<span data-ttu-id="5d565-718">在站点的 *web.config* 文件中使用 `system.webServer` 节点的 `aspNetCore` 部分配置 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="5d565-718">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="5d565-719">以下 *web.config* 文件发布用于[依赖框架的部署](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)，并配置 ASP.NET Core 模块以处理站点请求：</span><span class="sxs-lookup"><span data-stu-id="5d565-719">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="5d565-720">以下 web.config 发布用于[独立部署](/dotnet/articles/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="5d565-720">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="5d565-721">将应用部署为 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)时，`stdoutLogFile` 路径将设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="5d565-721">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5d565-722">该路径会将 stdout 日志保存到 *LogFiles* 文件夹，该文件夹是由服务自动创建的位置。</span><span class="sxs-lookup"><span data-stu-id="5d565-722">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="5d565-723">有关 IIS 子应用程序配置的信息，请参阅<xref:host-and-deploy/iis/index#sub-applications>。</span><span class="sxs-lookup"><span data-stu-id="5d565-723">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="5d565-724">aspNetCore 元素的属性</span><span class="sxs-lookup"><span data-stu-id="5d565-724">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="5d565-725">特性</span><span class="sxs-lookup"><span data-stu-id="5d565-725">Attribute</span></span> | <span data-ttu-id="5d565-726">描述</span><span class="sxs-lookup"><span data-stu-id="5d565-726">Description</span></span> | <span data-ttu-id="5d565-727">默认</span><span class="sxs-lookup"><span data-stu-id="5d565-727">Default</span></span> |
| ---
<span data-ttu-id="5d565-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-729">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-729">'Blazor'</span></span>
- <span data-ttu-id="5d565-730">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-730">'Identity'</span></span>
- <span data-ttu-id="5d565-731">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-731">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-732">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-732">'Razor'</span></span>
- <span data-ttu-id="5d565-733">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-733">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5d565-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-735">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-735">'Blazor'</span></span>
- <span data-ttu-id="5d565-736">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-736">'Identity'</span></span>
- <span data-ttu-id="5d565-737">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-737">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-738">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-738">'Razor'</span></span>
- <span data-ttu-id="5d565-739">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-739">'SignalR' uid:</span></span> 

<span data-ttu-id="5d565-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-741">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-741">'Blazor'</span></span>
- <span data-ttu-id="5d565-742">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-742">'Identity'</span></span>
- <span data-ttu-id="5d565-743">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-743">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-744">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-744">'Razor'</span></span>
- <span data-ttu-id="5d565-745">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-745">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5d565-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-747">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-747">'Blazor'</span></span>
- <span data-ttu-id="5d565-748">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-748">'Identity'</span></span>
- <span data-ttu-id="5d565-749">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-749">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-750">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-750">'Razor'</span></span>
- <span data-ttu-id="5d565-751">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-751">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5d565-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d565-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d565-753">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d565-753">'Blazor'</span></span>
- <span data-ttu-id="5d565-754">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d565-754">'Identity'</span></span>
- <span data-ttu-id="5d565-755">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d565-755">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d565-756">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d565-756">'Razor'</span></span>
- <span data-ttu-id="5d565-757">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5d565-757">'SignalR' uid:</span></span> 

<span data-ttu-id="5d565-758">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="5d565-758">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="5d565-759">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-759">Optional string attribute.</span></span></p><p><span data-ttu-id="5d565-760">processPath 中指定的可执行文件的参数。</span><span class="sxs-lookup"><span data-stu-id="5d565-760">Arguments to the executable specified in **processPath**.</span></span></p><span data-ttu-id="5d565-761">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="5d565-761">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="5d565-762">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-762">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5d565-763">如果为 true，将禁止显示“502.5 - 进程失败”页面，而会优先显示 web.config 中配置的 502 状态代码页面。</span><span class="sxs-lookup"><span data-stu-id="5d565-763">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="5d565-764">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="5d565-764">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="5d565-765">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-765">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5d565-766">如果为 true，会将令牌作为每个请求的标头“MS-ASPNETCORE-WINAUTHTOKEN”，转发到在 %ASPNETCORE_PORT% 上侦听的子进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-766">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="5d565-767">该进程负责在每个请求的此令牌上调用 CloseHandle。</span><span class="sxs-lookup"><span data-stu-id="5d565-767">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="5d565-768">| `true` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="5d565-768">| `true` | | `processesPerApplication` | </span></span><p><span data-ttu-id="5d565-769">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-769">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-770">指定每个应用均可启动的 **processPath** 设置中指定的进程的实例数。</span><span class="sxs-lookup"><span data-stu-id="5d565-770">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="5d565-771">不建议设置 `processesPerApplication`。</span><span class="sxs-lookup"><span data-stu-id="5d565-771">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="5d565-772">将来的版本将删除此属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-772">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="5d565-773">| 默认值：`1`</span><span class="sxs-lookup"><span data-stu-id="5d565-773">| Default: `1`</span></span><br><span data-ttu-id="5d565-774">最小值：`1`</span><span class="sxs-lookup"><span data-stu-id="5d565-774">Min: `1`</span></span><br><span data-ttu-id="5d565-775">最大值：`100` | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="5d565-775">Max: `100` | | `processPath` | </span></span><p><span data-ttu-id="5d565-776">必需的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-776">Required string attribute.</span></span></p><p><span data-ttu-id="5d565-777">为 HTTP 请求启动进程侦听的可执行文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-777">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="5d565-778">支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-778">Relative paths are supported.</span></span> <span data-ttu-id="5d565-779">如果路径以 `.` 开头，则该路径被视为与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5d565-779">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="5d565-780">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="5d565-780">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="5d565-781">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-781">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-782">指定允许 processPath 中指定的进程每分钟崩溃的次数。</span><span class="sxs-lookup"><span data-stu-id="5d565-782">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="5d565-783">如果超出了此限制，模块将在剩余分钟数内停止启动该进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-783">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> <span data-ttu-id="5d565-784">| 默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5d565-784">| Default: `10`</span></span><br><span data-ttu-id="5d565-785">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5d565-785">Min: `0`</span></span><br><span data-ttu-id="5d565-786">最大值：`100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="5d565-786">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="5d565-787">可选的 timespan 属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-787">Optional timespan attribute.</span></span></p><p><span data-ttu-id="5d565-788">指定 ASP.NET Core 模块等待来自 %ASPNETCORE_PORT% 上侦听的进程的响应的持续时间。</span><span class="sxs-lookup"><span data-stu-id="5d565-788">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="5d565-789">在 ASP.NET Core 2.1 或更高版本附带的 ASP.NET Core 模块版本中，使用小时数、分钟数和秒数指定 `requestTimeout`。</span><span class="sxs-lookup"><span data-stu-id="5d565-789">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> <span data-ttu-id="5d565-790">| 默认值：`00:02:00`</span><span class="sxs-lookup"><span data-stu-id="5d565-790">| Default: `00:02:00`</span></span><br><span data-ttu-id="5d565-791">最小值：`00:00:00`</span><span class="sxs-lookup"><span data-stu-id="5d565-791">Min: `00:00:00`</span></span><br><span data-ttu-id="5d565-792">最大值：`360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="5d565-792">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="5d565-793">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-793">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-794">检测到 app_offline.htm 文件时，模块等待可执行文件正常关闭的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5d565-794">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="5d565-795">| 默认值：`10`</span><span class="sxs-lookup"><span data-stu-id="5d565-795">| Default: `10`</span></span><br><span data-ttu-id="5d565-796">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5d565-796">Min: `0`</span></span><br><span data-ttu-id="5d565-797">最大值：`600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="5d565-797">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="5d565-798">可选的整数属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-798">Optional integer attribute.</span></span></p><p><span data-ttu-id="5d565-799">模块等待可执行文件启动端口上侦听的进程的持续时间（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5d565-799">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="5d565-800">如果超出了此时间限制，模块将终止该进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-800">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="5d565-801">模块在收到新请求时尝试重新启动该进程，并在收到后续传入请求时继续尝试重新启动该进程，除非应用在上一回滚分钟内无法启动 rapidFailsPerMinute 次。</span><span class="sxs-lookup"><span data-stu-id="5d565-801">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="5d565-802">值 0（零）不被视为无限超时。</span><span class="sxs-lookup"><span data-stu-id="5d565-802">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="5d565-803">| 默认值：`120`</span><span class="sxs-lookup"><span data-stu-id="5d565-803">| Default: `120`</span></span><br><span data-ttu-id="5d565-804">最小值：`0`</span><span class="sxs-lookup"><span data-stu-id="5d565-804">Min: `0`</span></span><br><span data-ttu-id="5d565-805">最大值：`3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="5d565-805">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="5d565-806">可选布尔属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-806">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="5d565-807">如果为 true，processPath 中指定的 进程的 stdout 和 stderr 将重定向到 stdoutLogFile 中指定的文件。</span><span class="sxs-lookup"><span data-stu-id="5d565-807">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="5d565-808">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="5d565-808">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="5d565-809">可选的字符串属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-809">Optional string attribute.</span></span></p><p><span data-ttu-id="5d565-810">指定在其中记录 processPath 中指定进程的 stdout 和 stderr 的相对路径或绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-810">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="5d565-811">相对路径与站点根目录相对。</span><span class="sxs-lookup"><span data-stu-id="5d565-811">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="5d565-812">以 `.` 开头的任何路径均与站点根目录相对，所有其他路径被视为绝对路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-812">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="5d565-813">路径中提供的任何文件夹都必须存在，以便模块创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="5d565-813">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="5d565-814">使用下划线分隔符，将时间戳、进程 ID 和文件扩展名 (.log) 添加到 stdoutLogFile 路径的最后一段。</span><span class="sxs-lookup"><span data-stu-id="5d565-814">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="5d565-815">如果 `.\logs\stdout` 作为值提供，则在示例 stdout 日志使用进程 ID 1934 于 2018 年 2 月 5 日 19:41:32 保存时，将在 logs 文件夹中保存为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="5d565-815">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="5d565-816">设置环境变量</span><span class="sxs-lookup"><span data-stu-id="5d565-816">Setting environment variables</span></span>

<span data-ttu-id="5d565-817">可以为 `processPath` 属性中的进程指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-817">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="5d565-818">使用 `<environmentVariables>` 集合元素的 `<environmentVariable>` 子元素指定环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-818">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="5d565-819">本部分中设置的环境变量与使用同一名称设置的系统环境变量冲突。</span><span class="sxs-lookup"><span data-stu-id="5d565-819">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="5d565-820">如果在 web.config 文件以及 Windows 中的系统级别中同时设置了环境变量，则 web.config 文件中的值将被追加到系统环境变量值（例如，`ASPNETCORE_ENVIRONMENT: Development;Development`），这将阻止应用启动。</span><span class="sxs-lookup"><span data-stu-id="5d565-820">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="5d565-821">以下示例设置了两个环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-821">The following example sets two environment variables.</span></span> <span data-ttu-id="5d565-822">`ASPNETCORE_ENVIRONMENT` 将应用的环境配置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="5d565-822">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="5d565-823">开发人员可能会暂时在 web.config 文件中设置此值，以便在调试应用异常时强制加载[开发人员异常页面](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="5d565-823">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="5d565-824">`CONFIG_DIR` 是用户定义的环境变量的一个示例，其中开发人员已写入可在启动时读取值的代码以便形成用于加载应用配置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="5d565-824">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="5d565-825">仅当不受信任的网络（如 Internet）无法访问暂存和测试服务器时，才能将 `ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="5d565-825">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="5d565-826">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="5d565-826">app_offline.htm</span></span>

<span data-ttu-id="5d565-827">如果在应用的根目录中检测到名为 “app_offline.htm”的文件，ASP.NET Core 模块将尝试正常关闭应用并停止处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-827">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="5d565-828">如果应用在 `shutdownTimeLimit` 中定义的秒数之后仍在运行，ASP.NET Core 模块将终止正在运行的进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-828">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="5d565-829">存在 app_offline.htm 文件时，ASP.NET Core 模块会通过发送回 app_offline.htm 文件的内容来响应请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-829">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="5d565-830">删除 app_offline.htm 文件后，下一个请求将启动应用。</span><span class="sxs-lookup"><span data-stu-id="5d565-830">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="5d565-831">启动错误页面</span><span class="sxs-lookup"><span data-stu-id="5d565-831">Start-up error page</span></span>

<span data-ttu-id="5d565-832">如果 ASP.NET Core 模块无法启动后端进程或后端进程启动但无法在配置的端口上侦听，则将显示“502.5 - 进程失败”状态代码页。</span><span class="sxs-lookup"><span data-stu-id="5d565-832">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="5d565-833">若要禁止显示此页面并还原为默认 IIS 502 状态代码页面，请使用 `disableStartUpErrorPage` 属性。</span><span class="sxs-lookup"><span data-stu-id="5d565-833">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="5d565-834">有关配置自定义错误消息的详细信息，请参阅 [HTTP 错误 \<httpErrors>](/iis/configuration/system.webServer/httpErrors/)。</span><span class="sxs-lookup"><span data-stu-id="5d565-834">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![“502.5 进程失败”状态代码页面](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="5d565-836">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="5d565-836">Log creation and redirection</span></span>

<span data-ttu-id="5d565-837">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="5d565-837">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="5d565-838">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="5d565-838">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="5d565-839">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\<app_pool_name>` 提供写入权限）。</span><span class="sxs-lookup"><span data-stu-id="5d565-839">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="5d565-840">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="5d565-840">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="5d565-841">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="5d565-841">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="5d565-842">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="5d565-842">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="5d565-843">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="5d565-843">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="5d565-844">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="5d565-844">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="5d565-845">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="5d565-845">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="5d565-846">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="5d565-846">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="5d565-847">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (.log) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 stdout）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="5d565-847">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="5d565-848">如果 `stdoutLogFile` 路径以 stdout 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 stdout_20180205194132_1934.log。</span><span class="sxs-lookup"><span data-stu-id="5d565-848">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="5d565-849">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="5d565-849">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="5d565-850">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="5d565-850">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="5d565-851">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="5d565-851">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="5d565-852">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="5d565-852">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="5d565-853">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="5d565-853">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="5d565-854">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="5d565-854">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="5d565-855">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="5d565-855">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="5d565-856">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="5d565-856">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="5d565-857">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="5d565-857">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="5d565-858">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="5d565-858">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="5d565-859">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-859">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="5d565-860">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="5d565-860">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="5d565-861">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="5d565-861">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="5d565-862">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-862">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="5d565-863">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="5d565-863">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="5d565-864">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="5d565-864">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="5d565-865">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="5d565-865">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="5d565-866">ASP.NET Core 模块安装程序使用 TrustedInstaller 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="5d565-866">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="5d565-867">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 applicationHost.config 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="5d565-867">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="5d565-868">使用 IIS 共享配置时，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="5d565-868">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="5d565-869">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="5d565-869">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="5d565-870">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="5d565-870">Run the installer.</span></span>
1. <span data-ttu-id="5d565-871">将已更新的 applicationHost.config 文件导出到共享。</span><span class="sxs-lookup"><span data-stu-id="5d565-871">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="5d565-872">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="5d565-872">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="5d565-873">模块版本和托管捆绑安装程序日志</span><span class="sxs-lookup"><span data-stu-id="5d565-873">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="5d565-874">若要确定已安装 ASP.NET Core 模块的版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5d565-874">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="5d565-875">在托管系统上，导航到 %windir%\System32\inetsrv。</span><span class="sxs-lookup"><span data-stu-id="5d565-875">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="5d565-876">找到 aspnetcore.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="5d565-876">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="5d565-877">右键单击该文件，然后从上下文菜单中选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="5d565-877">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="5d565-878">选择“详细信息”选项卡。“文件版本”和“产品版本”表示模块的已安装版本。</span><span class="sxs-lookup"><span data-stu-id="5d565-878">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="5d565-879">在 C:\\Users\\%UserName%\\AppData\\Local\\Temp 中找到了模块的托管捆绑安装程序日志。该文件名为 dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log。</span><span class="sxs-lookup"><span data-stu-id="5d565-879">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="5d565-880">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="5d565-880">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="5d565-881">模块</span><span class="sxs-lookup"><span data-stu-id="5d565-881">Module</span></span>

<span data-ttu-id="5d565-882">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="5d565-882">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="5d565-883">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-883">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="5d565-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="5d565-885">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="5d565-885">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="5d565-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="5d565-887">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="5d565-887">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="5d565-888">架构</span><span class="sxs-lookup"><span data-stu-id="5d565-888">Schema</span></span>

<span data-ttu-id="5d565-889">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5d565-889">**IIS**</span></span>

* <span data-ttu-id="5d565-890">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5d565-890">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="5d565-891">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5d565-891">**IIS Express**</span></span>

* <span data-ttu-id="5d565-892">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="5d565-892">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="5d565-893">Configuration</span><span class="sxs-lookup"><span data-stu-id="5d565-893">Configuration</span></span>

<span data-ttu-id="5d565-894">**IIS**</span><span class="sxs-lookup"><span data-stu-id="5d565-894">**IIS**</span></span>

* <span data-ttu-id="5d565-895">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5d565-895">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="5d565-896">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="5d565-896">**IIS Express**</span></span>

* <span data-ttu-id="5d565-897">Visual Studio：{APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="5d565-897">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="5d565-898">iisexpress.exe CLI：%USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="5d565-898">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="5d565-899">可以通过在 applicationHost.config 文件中搜索 aspnetcore 找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="5d565-899">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5d565-900">其他资源</span><span class="sxs-lookup"><span data-stu-id="5d565-900">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="5d565-901">[ASP.NET Core 模块引用源（主分支）](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2)：使用“分支”下拉列表选择特定版本（例如，`release/3.1`）。</span><span class="sxs-lookup"><span data-stu-id="5d565-901">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
